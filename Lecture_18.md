# UDP Client Server Communication

UDP does not need to establish a connection and hence not considered very reliable.

`Server.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>

int main()
{

	int serverSocket;

	serverSocket = socket(AF_INET, SOCK_DGRAM, 0);

	if (serverSocket == 0)
	{
		printf("Error creating server socket\n");
	}
	else
	{
		printf("Server socket created!\n");	
	}

	struct sockaddr_in svrAddress;
	svrAddress.sin_family = AF_INET;
	svrAddress.sin_addr.s_addr = INADDR_ANY;
	svrAddress.sin_port = htons(27000);

	int lenAddress = sizeof(svrAddress);

	int bindResult = bind(serverSocket, (struct sockaddr *)&svrAddress, lenAddress);
	if (bindResult < 0)
	{
		printf("Error binding to the socket\n");
	}
	else
	{
		printf("Socket bound successfully\n");
	}

	struct sockaddr_in client;
	int lenClient = sizeof(client);

	char RxBuffer[128];

	int received = recvfrom(serverSocket, RxBuffer, sizeof(RxBuffer), 0, (struct sockaddr*)&client, &lenClient);

	if (received)
	{
		printf("Received from the client--> %s\n", RxBuffer);
	}

	close(serverSocket);


	return 0;
}
```

`client.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include <arpa/inet.h>

int main()
{
	int clientSocket;

	clientSocket = socket(AF_INET, SOCK_DGRAM, 0);

	if (clientSocket < 0)
	{
		printf("Error initializing client socket\n");
	}
	else
	{
		printf("Client socket initialized!\n");
	}

	struct sockaddr_in svrAddress;
	svrAddress.sin_family = AF_INET;
	svrAddress.sin_port = htons(27000);
	svrAddress.sin_addr.s_addr = inet_addr("127.0.0.1");

	char TxBuffer[128] = {"Hello from Client"};

	int sent = sendto(clientSocket, TxBuffer, sizeof(TxBuffer), 0, (struct sockaddr *)&svrAddress, sizeof(svrAddress));

	if (sent)
	{
		printf("Message sent to the server\n");
	}
	
	close(clientSocket);

	return 0;
}
```

We can make our server keep running and accepting data by putting an infinite loop

`server.cpp`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<string.h>

int main()
{

	int serverSocket;

	serverSocket = socket(AF_INET, SOCK_DGRAM, 0);

	if (serverSocket == 0)
	{
		printf("Error creating server socket\n");
	}
	else
	{
		printf("Server socket created!\n");	
	}

	struct sockaddr_in svrAddress;
	svrAddress.sin_family = AF_INET;
	svrAddress.sin_addr.s_addr = INADDR_ANY;
	svrAddress.sin_port = htons(27000);

	int lenAddress = sizeof(svrAddress);

	int bindResult = bind(serverSocket, (struct sockaddr *)&svrAddress, lenAddress);
	if (bindResult < 0)
	{
		printf("Error binding to the socket\n");
	}
	else
	{
		printf("Socket bound successfully\n");
	}

	struct sockaddr_in client;
	int lenClient = sizeof(client);
	char RxBuffer[128];

	while (1)
	{
		
		memset(RxBuffer, 0, sizeof(RxBuffer));
		int received = recvfrom(serverSocket, RxBuffer, sizeof(RxBuffer), 0, (struct sockaddr*)&client, &lenClient);

		if (received)
		{
			printf("Received from the client--> %s\n", RxBuffer);
		}
	}

	close(serverSocket);

	return 0;
}
```

Client code would remain same.

We can send several messages from Client to Server.

`server.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<string.h>
#include <arpa/inet.h>

int main()
{

	int serverSocket;

	serverSocket = socket(AF_INET, SOCK_DGRAM, 0);

	if (serverSocket == 0)
	{
		printf("Error creating server socket\n");
	}
	else
	{
		printf("Server socket created!\n");	
	}

	struct sockaddr_in svrAddress;
	svrAddress.sin_family = AF_INET;
	svrAddress.sin_addr.s_addr = INADDR_ANY;
	svrAddress.sin_port = htons(8080);

	int lenAddress = sizeof(svrAddress);

	int bindResult = bind(serverSocket, (struct sockaddr *)&svrAddress, lenAddress);
	if (bindResult < 0)
	{
		printf("Error binding to the socket\n");
	}
	else
	{
		printf("Socket bound successfully\n");
	}

	struct sockaddr_in client;
	int lenClient = sizeof(client);
	char RxBuffer[128];

	while (1)
	{
		memset(RxBuffer, 0, sizeof(RxBuffer));
		int received = recvfrom(serverSocket, RxBuffer, sizeof(RxBuffer), 0, (struct sockaddr*)&client, &lenClient);

		char * clientIP = inet_ntoa(client.sin_addr);
		if (received)
		{
			printf("Received from the client with IP: %s --> %s\n", clientIP, RxBuffer);

		}
	}
	close(serverSocket);
	return 0;
}
```

`client.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include <arpa/inet.h>

int main()
{
	int clientSocket;

	clientSocket = socket(AF_INET, SOCK_DGRAM, 0);

	if (clientSocket < 0)
	{
		printf("Error initializing client socket\n");
	}
	else
	{
		printf("Client socket initialized!\n");
	}

	struct sockaddr_in svrAddress;
	svrAddress.sin_family = AF_INET;
	svrAddress.sin_port = htons(8080);
	svrAddress.sin_addr.s_addr = inet_addr("127.0.0.1");

	char TxBuffer[5];

	for (int i=0; i<5; i++)
	{
		sleep(1);
		sprintf(TxBuffer,"%d", i);
		int sent = sendto(clientSocket, TxBuffer, sizeof(TxBuffer), 0, (struct sockaddr *)&svrAddress, sizeof(svrAddress));

		if (sent)
		{
			printf("%d sent to the server\n", i);
		}
	}

	close(clientSocket);

	return 0;
}
```
Client can be updated to send a random number instead
```
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include <arpa/inet.h>

int main()
{
	int clientSocket;

	clientSocket = socket(AF_INET, SOCK_DGRAM, 0);

	if (clientSocket < 0)
	{
		printf("Error initializing client socket\n");
	}
	else
	{
		printf("Client socket initialized!\n");
	}

	struct sockaddr_in svrAddress;
	svrAddress.sin_family = AF_INET;
	svrAddress.sin_port = htons(27000);
	svrAddress.sin_addr.s_addr = inet_addr("127.0.0.1");

	char TxBuffer[128] = {};
	int number;

	for (int i=0; i<10; i++)
	{
		sleep(1);
		number = (rand() % 100) + 1;
		sprintf(TxBuffer, "%d", number);
		int sent = sendto(clientSocket, TxBuffer, sizeof(TxBuffer), 0, (struct sockaddr *)&svrAddress, sizeof(svrAddress));

		if (sent)
		{
			printf("Message sent to the server --> %d\n", number);
		}
	}

	
	
	close(clientSocket);

	return 0;
}

```
