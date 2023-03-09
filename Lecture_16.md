# Listing Network Adapters

We can write a C program that lists all the network adapters using ifaddr and netdb libraries
```
#include<stdio.h>
#include<stdlib.h>
#include<ifaddrs.h>
#include<sys/socket.h>
#include<netdb.h>

int main()
{
  struct ifaddrs *addresses;
  if (getifaddrs(&addresses) == -1)
  {
    printf("Error getting network info\n");
  }
  struct ifaddrs *address = addresses;
  while (address)
  {
  char name[100];
  int family = address->ifa_addr->sa_family;
  printf("Family: %d\n", family);

  int size = sizeof(struct sockaddr_in);
  getnameinfo(address->ifa_addr, size, name, sizeof(name), 0, 0, 0);
  printf("Interface Name: %s\n", name);
  address = address->ifa_next;
  }
  return 0;
}
```
