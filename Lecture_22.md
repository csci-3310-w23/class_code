# Operating System Security

Port Scanning is a technique used to make sure no extra port is open
```
#include<stdio.h>
#include<stdlib.h>
#include<sys/socket.h>
#include<unistd.h>
#include <arpa/inet.h>

int main(int argc, char *argv[])
{

	int start = atoi(argv[1]);
	int end = atoi(argv[2]);

	int sock;
	struct sockaddr_in svrAddress;

	for (int i=start; i<end; i++)
	{
		sock = socket(AF_INET, SOCK_STREAM, 0);
		if (sock == -1)
		{
			printf("Error on this socket\n");
		}

		svrAddress.sin_family = AF_INET;
		svrAddress.sin_port = htons(i);

		if (connect(sock, (struct sockaddr *)&svrAddress, sizeof(svrAddress)) == 0)
		{
			printf("Port %d is open\n", i);
		}
		else
		{
			printf("Port %d is closed\n", i);	
		}
		close(sock);
	}
	return 0;
}
```
Sometimes it is a good idea to check if a specific service is running or not. Below is the program that checks if sshd is running or not?

In terminal we can use the following command to check for all the running services
systemctl list-units --type=service

A C Program can be written using the following code
```
#include<stdio.h>
#include<stdlib.h>
#include<string.h>

int main()
{
	char command[1024];
	snprintf(command, 1024, "systemctl is-active sshd");

	FILE *ptr = popen(command, "r");

	char line[1024];

	if (fgets(line, 1024, ptr) != NULL)
	{
		line[strcspn(line, "\r\n")] = 0;
		if (strcmp(line, "inactive") == 0)
		{
			printf("SSH Service is in-active\n");
		}
		else
		{
			printf("SSH Service is active\n");
		}
	}
	return 0;
}
```
Protection can be used to ensure  privacy 
```
#include<stdio.h>
#include<stdlib.h>

int main()
{
	FILE *file;

	file = fopen("/etc/shadow", "r");

	if (file == NULL)
	{
		printf("Error opening file\n");
		exit(1);
	}
	printf("File opened successfully");

	return 0;
}
```
Above program can be updated to check for a specific error
```
#include<stdio.h>
#include<stdlib.h>
#include<errno.h>
#include<string.h>

int main()
{
	FILE *file;

	file = fopen("/etc/shadow", "r");

	if (file == NULL)
	{
		if (errno == EACCES)
		{
			printf("Permission Denied\n");
		}
		else
		{
			printf("Error. %s\n", strerror(errno));
		}
		
		exit(1);
	}
	printf("File opened successfully");

	return 0;
}
```
We can write a C Program that helps us add rules to our firewall

Below command can be used to list all Firewall rules
`sudo iptables -L -n`

In order to add IP rules following command can be used which would drop all the packets from give ip address
`sudo /sbin/iptables -I INPUT -s 192.168.1.22 -j DROP`

In order to remove IP rule, below command can be used
`sudo /sbin/iptables -I INPUT -s 192.168.1.22 -j DROP`
```
#include <stdio.h>
#include <stdlib.h>

int main() {
    char *ip_address = "192.168.1.22";

    char command[1024];
    snprintf(command, sizeof(command), "/sbin/iptables -I INPUT -s %s -j DROP", ip_address);

    if (system(command) == -1) {
        perror("system");
        exit(1);
    }

    printf("Firewall rule created to drop packets from %s\n", ip_address);

    return 0;
}
```
In order to remove the firewall rule we just added, we can modify our program as below:
```
#include <stdio.h>
#include <stdlib.h>

int main() {
    char *ip_address = "192.168.1.22";

    char command[1024];
    snprintf(command, sizeof(command), "/sbin/iptables -D INPUT -s %s -j DROP", ip_address);

    if (system(command) == -1) {
        perror("system");
        exit(1);
    }

    printf("Firewall rule removed to drop packets from %s\n", ip_address);

    return 0;
}
```
