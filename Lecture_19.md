# Multi-thread Servers

In our previous code, our Server could handle only one client at a time

`server.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<string.h>

int main()
{
	int serverSocket;

	serverSocket = socket(AF_INET, SOCK_STREAM, 0);

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

	int listenResult = listen(serverSocket, 1);
	if (listenResult < 0)
	{
		printf("Error liistening to the socket\n");
	}
	else
	{
		printf("Listening to socket\n");
	}

	int clientSocket;
	char RxBuffer[128] = {};
	char TxBuffer[128] = {"Acknowledging your message"};

	clientSocket= accept(serverSocket, (struct sockaddr *)&svrAddress, (socklen_t *)&lenAddress);
	if (clientSocket < 0)
	{
		printf("Error accepting connections\n");
	}
	else
	{
		printf("Accepting new connections!\n");
	}
	
	printf("Connection Established\n");
	
	while (1)
	{
		memset(RxBuffer, 0, sizeof(RxBuffer));
		int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

		if (received > 0)
		{
			printf("Received from Client --> %s\n", RxBuffer);
		}
	}

	close(clientSocket);
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
#include<string.h>

int main()
{
	int clientSocket;

	clientSocket = socket(AF_INET, SOCK_STREAM, 0);

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

	int connectResult = connect(clientSocket, (struct sockaddr *)& svrAddress, sizeof(svrAddress));
	if (connectResult < 0)
	{
		printf("Error establishing connection\n");
	}
	else
	{
		printf("Connection established\n");
	}

	char TxBuffer[128] = {"Hello from client"};
	char RxBuffer[128] = {};

	for (int i = 0; i < 100; i++) 
	{
		sleep(1);
		sprintf(TxBuffer,"%d", i);
	    int sent = send(clientSocket, TxBuffer, strlen(TxBuffer), 0);
        printf("Message %d sent to server\n", i);
	}

	close(clientSocket);

	return 0;
}
```

Multithreaded servers are handy in entertaining several clients at the same time

`server.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<string.h>
#include<pthread.h>

// Method to run on each thread
void *manage_clients(void * args)
{
	// Getting the client socket by casting it back to an integer
	int clientSocket = *(int *)args;

	// A buffer to store the information received from the client
	char RxBuffer[1024] = {};

	// Loop to keep receiving information from the client
	while(1)
	{
		// receiving data from the client in RxBuffer
		int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

		// This means no bytes received and hence client is expected to have disconnected.
		if (received == 0)
		{
			printf("Client Disconnected\n");
			break;
		}
		else 
		{
			// Showing data received from the client
			printf("Received from the Client --> %s\n", RxBuffer);

			// Sending back the acknowledgement to the client
			send(clientSocket, "Ack", 4, 0);
		}
	}
	//closing client socket
	close(clientSocket);

	// Exiting the thread without passing any information back to the main thread
	pthread_exit(NULL);
}

int main()
{

	int serverSocket;

	// creating server socket with TCP
	serverSocket = socket(AF_INET, SOCK_STREAM, 0);

	if (serverSocket == 0)
	{
		printf("Error creating server socket\n");
	}
	else
	{
		printf("Server socket created!\n");	
	}

	// creating server information to be passed to the bind function
	struct sockaddr_in svrAddress;
	// using IPv4 family
	svrAddress.sin_family = AF_INET;

	// using current loopback address
	svrAddress.sin_addr.s_addr = INADDR_ANY;

	// using port 27000 by converting it into bytes to transfer over the network
	svrAddress.sin_port = htons(27000);

	int lenAddress = sizeof(svrAddress);

	// binding the socket 
	int bindResult = bind(serverSocket, (struct sockaddr *)&svrAddress, lenAddress);
	if (bindResult < 0)
	{
		printf("Error binding to the socket\n");
	}
	else
	{
		printf("Socket bound successfully\n");
	}

	// listening at the server socket with 1 client in queue
	int listenResult = listen(serverSocket, 1);
	if (listenResult < 0)
	{
		printf("Error listening to the socket\n");
	}
	else
	{
		printf("Listening to socket\n");
	}

	// creating socket for client
	int clientSocket;
	char RxBuffer[128] = {};
	
	// Infinite loop to make server run indefinitely
	while (1)
	{
		// accepting incoming client connections
		clientSocket= accept(serverSocket, (struct sockaddr *)&svrAddress, (socklen_t *)&lenAddress);
		if (clientSocket < 0)
		{
			printf("Error accepting connections\n");
		}
		else
		{
			printf("Accepting new connections!\n");
		}
	
		printf("Connection Established\n");

		pthread_t tid;

		// creating a thread by passing it a method and a parameter to work on
		if (pthread_create(&tid, NULL, manage_clients, (void *)&clientSocket) != 0)
		{
			printf("Error creating thread\n");
		}
		
	}

	// closing server socket
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
#include<string.h>

int main()
{
	int clientSocket;

	clientSocket = socket(AF_INET, SOCK_STREAM, 0);

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

	int connectResult = connect(clientSocket, (struct sockaddr *)& svrAddress, sizeof(svrAddress));
	if (connectResult < 0)
	{
		printf("Error establishing connection\n");
	}
	else
	{
		printf("Connection established\n");
	}
	char TxBuffer[5];
	char buffer[1024] = {0};
	for (int i = 0; i < 100; i++) 
	{
		sleep(1);
		sprintf(TxBuffer,"%d", i);
	        int sent = send(clientSocket, TxBuffer, strlen(TxBuffer), 0);
        	printf("Message %d sent to server\n", i+1);
	}

	    recv(clientSocket, buffer, 1024, 0);
	    printf("Server response: %s\n", buffer);

	    close(clientSocket);

	return 0;
}
```
Instead of sending data in the form of integers, we can also send data which is a collection like a struct.
`client.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include <arpa/inet.h>
#include<string.h>

struct Vehicle
{	
	char name[10];
	int kms;
};

int main()
{
	int clientSocket;

	clientSocket = socket(AF_INET, SOCK_STREAM, 0);

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

	int connectResult = connect(clientSocket, (struct sockaddr *)& svrAddress, sizeof(svrAddress));
	if (connectResult < 0)
	{
		printf("Error establishing connection\n");
	}
	else
	{
		printf("Connection established\n");
	}

	char TxBuffer[128] = {};

	struct Vehicle vehicle = {"Toyota", 1234};
	sprintf(TxBuffer, "%s,%d",vehicle.name, vehicle.kms);
	
	int sent = send(clientSocket, TxBuffer, strlen(TxBuffer), 0);

	close(clientSocket);

	return 0;
}
```
