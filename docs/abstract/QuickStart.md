# Quickstart

Usually with the Field Network Director, you'll get access to an OVA file from Cisco with instructions on the installation. 
This installation will create a FND server with an Oracle database. This Image must be installed on either an ESXi VMWare for enterprise use, or a regular VMware for the use of testing and development. The procedure of installation can be found within the [OpenCSMP repo documentation](https://github.com/CiscoDevNet/csmp-agent-lib/blob/main/docs/CSMP%20Developer%20Tutorial%20-%200v11.pdf) 

# Troubleshooting

There are a few things we noticed while doing our installation that might be useful

1. Have a unique device ID.

If you are trying to install a server on multiple devices throughout a network, you may run into an issue when it comes to your device IDs. One way we resolved this was through removing our old IDs and generating new ones:

// Remove old id
sudo rm /etc/machine-id

// generate new machine id
dbus-uuidgen --ensure
sudo systemd-machine-id-setup

2. Check server logs.

As said within the OpenCSMP repo documentation, there is a directory you can use to check the logs for installlation (tail -F /opt/cgms/server/cgms/log/server.log)

3. Using IPv6 versuse IPv4 and vice versa.

Make sure your VM is using the correct IP addresses while working with the server, elsewise some devices will not be able to access the server.
