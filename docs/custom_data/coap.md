# CSMP‑Agent‑Lib: CoAP Server & Air‑Pollution API

This document covers only the **CoAP server** aspects of `csmp-agent-lib`, which the agent device uses to **serve** or **proxy** air‑pollution data from an external CoAP server.

---

## Table of Contents

- [CoAP Server API](#coap-server-api)  
  - [Core Types](#core-types-coaph)  
  - [Server Functions](#server-functions-coapserverh)  
  - [Request Callback](#request-callback-coapserverh)  
  - [Observable Resources](#observable-resources-coapserverh)  
- [Air‑Pollution Module](#air-pollution-module)  
  - [Data Types & (De)Serialization](#data-types-serialization-air_pollutionh)  
  - [Pollution‑Agent API](#pollution-agent-api-pollution_agentc)  
- [Quick Start](#quick-start)

---

## CoAP Server API

The agent device runs a CoAP server to respond to GET requests or to notify observers when its local air‑pollution data changes.

<a name="core-types-coaph"></a>
### Core Types (`src/coap/coap.h`)
```c
// CoAP header (packed)
typedef struct {
  uint8_t control;     /* version, type, token length */
  uint8_t code;        /* method or response code */
  uint16_t message_id; /* message ID (network‐order) */
} __attribute__((packed)) coap_header_t;

// URL segment
typedef struct {
  uint32_t len;  /* length of segment */
  uint8_t *val;  /* pointer to raw segment bytes */
} coap_uri_seg_t;

// Transaction types
enum {
  COAP_CON = 0,  /* confirmable */
  COAP_NON = 1,  /* non-confirmable */
  COAP_ACK = 2,  /* acknowledgement */
  COAP_RST = 3   /* reset */
};

// CoAP status codes (select list)
enum {
  COAP_CODE_OK                  = 200,  /* 2.00 */
  COAP_CODE_CONTENT             = 205,  /* 2.05 */
  COAP_CODE_BAD_REQ             = 400,  /* 4.00 */
  COAP_CODE_NOT_FOUND           = 404,  /* 4.04 */
  COAP_CODE_METHOD_NOT_ALLOWED  = 405,  /* 4.05 */
  COAP_CODE_INTERNAL_SERVER_ERROR = 500 /* 5.00 */
};

#define COAP_PAYLOAD_MARKER  0xFF
#define COAP_MAX_TKL         8
#define COAP_RESPONSE_CODE(N) (((N)/100 << 5) | ((N)%100))
```
[View `coap.h`](src/coap/coap.h)

---

<a name="server-functions-coapserverh"></a>
### Server Functions (`src/coap/coapserver.h`)

```c
// Start a UDP-based CoAP server on `port`.
// Incoming requests are delivered to `recv_handler`.
int coapserver_listen(uint16_t port,
                      recv_handler_t recv_handler);

// Stop the CoAP server (close socket, free resources).
// Returns 0 on success.
int coapserver_stop(void);

// Send a CoAP response back to client.
// - to:        client address
// - tx_type:   CON | NON | ACK | RST
// - tx_id:     message_id from incoming request
// - token_length/token: opaque token from request
// - status:    COAP_RESPONSE_CODE(<coap_code_t>)
// - body/body_len: optional payload
int coapserver_response(const struct sockaddr_in6 *to,
                        coap_transaction_type_t tx_type,
                        uint16_t tx_id,
                        uint8_t token_length,
                        uint8_t *token,
                        uint16_t status,
                        const void *body,
                        uint16_t body_len);
```
[View `coapserver.h`](src/coap/coapserver.h)

---

<a name="request-callback-coapserverh"></a>
### Request Callback (`src/coap/coapserver.h`)

Define your request handler with this signature:

```c
typedef void (*recv_handler_t)(
    struct sockaddr_in6      *from,
    coap_transaction_type_t   tx_type,
    uint16_t                  tx_id,
    uint8_t                   token_length,
    uint8_t                 * token,
    coap_method_t             method,
    const coap_uri_seg_t     *url,
    uint32_t                  url_cnt,
    const coap_uri_seg_t     *query,
    uint32_t                  query_cnt,
    const void               *body,
    uint16_t                  body_len
);
```

**Example implementation** in `pollution_agent.c`:

```c
static void coap_request_handler(
    struct sockaddr_in6 *from,
    coap_transaction_type_t tx_type,
    uint16_t tx_id,
    uint8_t token_length,
    uint8_t *token,
    coap_method_t method,
    const coap_uri_seg_t *url,
    uint32_t url_cnt,
    const coap_uri_seg_t *query,
    uint32_t query_cnt,
    const void *body,
    uint16_t body_len
) {
    // 1. Match URL segments: e.g. url_cnt==2 && strcmp(url[0],"pollution")==0 && strcmp(url[1],"data")==0
    // 2. On GET: serialize current data and call coapserver_response(...)
    // 3. Otherwise: respond COAP_CODE_NOT_FOUND
}
```
[View `coapserver.c`](src/coap/coapserver.c)

---

<a name="observable-resources-coapserverh"></a>
### Observable Resources

To support CoAP Observe (server pushes updates when data changes):

```c
// Declare that a given resource path is observable.
int coapserver_register_observable(const coap_uri_seg_t *url,
                                   uint32_t url_cnt);

// Notify all registered observers of that resource.
int coapserver_notify_observers(const coap_uri_seg_t *url,
                                uint32_t url_cnt,
                                const void *body,
                                uint16_t body_len);
```

- Call `register_observable()` during initialization for `/pollution/data`.
- When local data updates, call `notify_observers()` with the new serialized payload.

---

## Air‑Pollution Module

Contains data structures and helpers for serializing sensor readings.

<a name="data-types-serialization-air_pollutionh"></a>
### Data Types & (De)Serialization (`src/airpollution/air_pollution.h`)

```c
// Bundle of sensor metrics
typedef struct {
  float pm2_5;
  float pm10;
  float co2;
  // … add more as needed …
} air_pollution_data_t;

// Serialize `data` into `out[0..out_len)`.
// Returns bytes written or -1 on error.
int air_pollution_serialize(const air_pollution_data_t *data,
                            uint8_t *out, size_t out_len);

// Deserialize buffer into `out`.
// Returns 0 on success or -1 on error.
int air_pollution_deserialize(const uint8_t *buf,
                              size_t buf_len,
                              air_pollution_data_t *out);
```
[View `air_pollution.h`](src/airpollution/air_pollution.h)

---

<a name="pollution-agent-api-pollution_agentc"></a>
### Pollution‑Agent API (`src/airpollution/pollution_agent.c`)

```c
// Initialize CoAP server + hook request handler
// - server_addr: IPv6 to bind (e.g. in6addr_any)
// - port: UDP port (e.g. 5684)
int pollution_agent_init_with_port(const struct in6_addr *server_addr,
                                   uint16_t port);

// Shortcut: default port 5684
typedef int pollution_agent_init(const struct in6_addr *server_addr);

// Start listening & serving requests
int pollution_agent_start(void);

// Stop serving & cleanup
int pollution_agent_stop(void);

// Install a local callback to be invoked when data is served or updated
void pollution_agent_set_callback(void (*cb)(const air_pollution_data_t *data));
```

**Internal flow**:
1. `init()` calls `coapserver_listen()` with `coap_request_handler`.  
2. `start()` enters event loop (or spawns thread).  
3. On GET `/pollution/data`: reads current `air_pollution_data_t`, serializes, and calls `coapserver_response(..., COAP_CODE_CONTENT, payload, len)`.  
4. If observers registered, `coapserver_notify_observers()` is invoked.

[View `pollution_agent.c`](src/airpollution/pollution_agent.c)  
[View `pollution_agent.h`](src/airpollution/pollution_agent.h)

---

## Quick Start

```bash
# Build the library and samples
cd csmp-agent-lib
make clean && make

# Run the pollution agent server (binds to ::, port 5684)
./bin/pollution_agent_server --port 5684
```

- Use any CoAP client (e.g., `coap-client -m get coap://[::1]:5684/pollution/data`) to fetch current readings.  
- To observe, use `coap-client -m get -s 0 coap://[::1]:5684/pollution/data` and watch notifications.

---

