# Networking in Linux

1. `ping` helps in checking the network connectivity between host and server/host

`ping ontariotechu.ca`


2. `ifconfig` is handy to work with your interfaces. In its simplest usage, all you need to do is provide the name of the interface being configured and the IP address

`ifconfig`

3. `ip` is a new utility built on top of ifconfig

`ip addr`

4. `netstat` program is used to display the status of all of the network connections on a host

`netstat`

5. `tracepath` traces the route to the specified destination and identifies each hop in it. If your network is weak, it recognizes the point where the network is weak.

`tracepath google.com`

6. `nslookup` is a command used for DNS related queries

`nslookup ontariotechu.ca`

7. `host` command displays the domain name for a given IP address and IP address for a given hostname

`host ontariotechu.ca`
