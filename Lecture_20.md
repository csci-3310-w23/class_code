# HTTP Server

A Web Server uses HTTP over TCP to communicate with the client. 

`server.c`
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<string.h>
#include<pthread.h>

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

	int listenResult = listen(serverSocket, 10);
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
	//char TxBuffer[128];

	const char *TxBuffer = "HTTP/1.1 200 OK\nContent-Type: text/plain\nContent-Length: 11\n\nHello World";
	//snprintf(TxBuffer, sizeof(TxBuffer), "HTTP/1.1 200 OK\nContent-Type: text/plain\nContent-Length: %lu\n\n%s", strlen("Hello"), "Hello");

	int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

	printf("Received from Client:\n%s\n", RxBuffer);

	send(clientSocket, TxBuffer, strlen(TxBuffer), 0);
	printf("Response sent to the client!\n%s\n", TxBuffer);

	close(clientSocket);
	close(serverSocket);
	return 0;
}
```

Instead of simply sending the "Hello World" message, we can send something meaningful like current system time.
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<string.h>
#include<pthread.h>

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

	int listenResult = listen(serverSocket, 10);
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
	char TxBuffer[128];

	time_t timer = time(NULL);

	char *time_str = ctime(&timer);

	snprintf(TxBuffer, sizeof(TxBuffer), "HTTP/1.1 200 OK\nContent-Type: text/plain\nContent-Length: %lu\n\n%s", strlen(time_str), time_str);

	int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

	printf("Received from Client:\n%s\n", RxBuffer);

	send(clientSocket, TxBuffer, strlen(TxBuffer), 0);
	printf("Response sent to the client!\n%s\n", TxBuffer);

	close(clientSocket);
	close(serverSocket);
	return 0;
}
```

Servers normally send back html. We can do that by modifying our server a bit
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<stdlib.h>
#include<string.h>
#include<pthread.h>

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

	int listenResult = listen(serverSocket, 10);
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

	char RxBuffer[1024] = {};
	char fileBuffer[1024];

	const char *TxBuffer = "HTTP/1.1 200 OK\nContent-Type: text/html\n\n<html><body><h1>Hello, World!</h1></body></html>";

	int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

	printf("Received from Client:\n%s\n", RxBuffer);

	send(clientSocket, TxBuffer, strlen(TxBuffer), 0);
	printf("Response sent to the client!\n%s\n", TxBuffer);


	close(clientSocket);

	close(serverSocket);
	return 0;
}
```

Servers can send back HTML files as well
```
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<string.h>
#include<pthread.h>

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

	int listenResult = listen(serverSocket, 10);
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

	char RxBuffer[1024] = {};
	
	int received = recv(clientSocket, RxBuffer, sizeof(RxBuffer), 0);

	printf("Received from Client:\n%s\n", RxBuffer);

	FILE *file;
	char *html_buffer;

	file = fopen("index.html", "r");
	fseek(file, 0, SEEK_END);
	long file_size = ftell(file);
	rewind(file);

	html_buffer = malloc(file_size + 1);
	fread(html_buffer, file_size, 1, file);
	fclose(file);

	char * http_header = "HTTP/1.1 200 OK\nContent-Type: text/html\r\n\r\n";
	send(clientSocket, http_header, strlen(http_header), 0);
	send(clientSocket, html_buffer, file_size, 0);
	printf("Response sent to the client!\n%s\n", html_buffer);

	close(clientSocket);
	close(serverSocket);
	return 0;
}
```
We can also send image files from the Server using the following header:

`char* http_header = "HTTP/1.1 200 OK\r\nContent-Type: image/jpeg\r\n\r\n";`
