# Socket Programming

Socket Programming can be used to perform Server Client Communication

`Server.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>

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

	int clientSocket = accept(serverSocket, (struct sockaddr *)&svrAddress, (socklen_t *)&lenAddress);
	if (clientSocket < 0)
	{
		printf("Error accepting connections\n");
	}
	else
	{
		printf("Accepting new connections!\n");
	}

	printf("Connection Established\n");

	char RxBuffer[128] = {};
	char TxBuffer[128] = {"Acknowledging your message"};

	int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

	if (received > 0)
	{
		printf("Received from Client --> %s\n", RxBuffer);

		send(clientSocket, TxBuffer, sizeof(TxBuffer), 0);
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

	send(clientSocket, TxBuffer, sizeof(TxBuffer), 0);
	printf("Message sent to the server\n");

	int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

	if (received > 0)
	{
		printf("Received from Server --> %s\n", RxBuffer);
	}

	close(clientSocket);

	return 0;
}
```
