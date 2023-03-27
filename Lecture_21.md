# Secure Communication (OpenSSL)

In order to work with openSSL, it is necessary to check if openSSL is installed or not? Below command can be used for that

`openssl version`

Below command can be used to install openssl if not installed already

`sudo apt install openssl`

In order to use openSSL, it is important in install its headers which can be done using the following command in Linux terminal:

`sudo apt-get install libssl-dev`

Once above steps are done, it is now time to generate certificates and private key which can be done as below:

1. Generate a Key

`openssl genrsa -out key.pem 2048`


2. Generate a certificate using the key above

`openssl req -new -key key.pem -out request.pem`

3. Next step would be to sign the certificate

`openssl x509 -req -days 30 -in request.pem -signkey key.pem -out certificate.pem`

Once these steps are done, we can now write Server and Client code in C
```
server_ssl.c

#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include<openssl/ssl.h>
#include <openssl/err.h>


int main()
{
	printf("Initializing SSL\n");
	SSL_library_init();
	printf("Loading Error Settings\n");
	SSL_load_error_strings();
	printf("Loading Algorithms\n");
	OpenSSL_add_all_algorithms();

	SSL_CTX *ctx;

	printf("Setting SSL Context\n");
	ctx = SSL_CTX_new(SSLv23_server_method());
	//ctx = SSL_CTX_new(TLSv1_2_server_method());

	if (!ctx)
	{
		printf("Error creating SSL context\n");
	}

	printf("Using Certificate\n");
	if (SSL_CTX_use_certificate_file(ctx, "certificate.pem", SSL_FILETYPE_PEM) <= 0) // PEM = Privacy Enhanced Mail
	{
		fprintf (stderr, "SSL_CTX_new ERROR\n");
        ERR_print_errors_fp(stderr);	
	}
	printf("Using Private Key\n");
	if(SSL_CTX_use_PrivateKey_file(ctx, "key.pem", SSL_FILETYPE_PEM) <= 0)
	{
		fprintf (stderr, "SSL_CTX_new ERROR\n");
        ERR_print_errors_fp(stderr);
	}

	int serverSocket = socket(AF_INET, SOCK_STREAM, 0);
	if (serverSocket < 0)
	{
		printf("Error creating socket\n");	
	}

	struct sockaddr_in svrAddress;
	svrAddress.sin_family = AF_INET;
	svrAddress.sin_addr.s_addr = INADDR_ANY;
	svrAddress.sin_port = htons(27000);

	int lenAddress = sizeof(svrAddress);

	if (bind(serverSocket, (struct sockaddr *)&svrAddress, sizeof(svrAddress)) < 0)
	{
		printf("Error binding socket\n");	
	}

	if (listen(serverSocket, 1) < 0)
	{
		printf("Error listening to the socket\n");	
	}

	struct sockaddr_in client;
	socklen_t lenClient = sizeof(client);

	int clientSocket = accept(serverSocket, (struct sockaddr*)&client, &lenClient);

	if (clientSocket < 0)
	{
		printf("Error accepting connection\n");
	}
	else
	{
		printf("Accepting connection\n");
	}

	printf("Connection Established\n");

	SSL *ssl = SSL_new(ctx);
	SSL_set_fd(ssl, clientSocket);

	if (SSL_accept(ssl) <= 0)
	{
		printf("Error creating SSL connection\n");	
	}

	char RxBuffer[1024] = {};

	int i = SSL_read(ssl, RxBuffer, sizeof(RxBuffer));
	printf("Received %i bytes\n", i);
	printf("Received from Client --> %s\n", RxBuffer);

	SSL_write(ssl, "Hello from Server", sizeof("Hello from Server"));

	SSL_shutdown(ssl);
	SSL_free(ssl);
	SSL_CTX_free(ctx);
	close(clientSocket);
	close(serverSocket);

	return 0;
}
```

Run above program using this 
`gcc server_ssl.c -o server_ssl.o -lssl -lcrypto`

client_ssl.c
```
#include<stdio.h>
#include<unistd.h>
#include<sys/socket.h>
#include<netdb.h>
#include <arpa/inet.h>
#include<openssl/ssl.h>

int main()
{
	SSL_library_init();
	SSL_load_error_strings();
	OpenSSL_add_all_algorithms();

	SSL_CTX *ctx;

	ctx = SSL_CTX_new(TLS_client_method());
	//ctx = SSL_CTX_new(TLSv1_2_client_method());

	if (!ctx)
	{
		printf("Error creating SSL context\n");
	}

	/* Load server certificate */
    if (SSL_CTX_load_verify_locations(ctx, "certificate.pem", NULL) <= 0) {
        printf("Error loading server certificate\n");
        exit(1);
    }

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

	int connectResult = connect(clientSocket, (struct sockaddr *)&svrAddress, sizeof(svrAddress));
	if (connectResult < 0)
	{
		printf("Error establishing connection\n");
	}
	else
	{
		printf("Connection established\n");
	}

	SSL *ssl = SSL_new(ctx);
	SSL_set_fd(ssl, clientSocket);

	if (SSL_connect(ssl) <= 0)
	{
		printf("Error creating SSL connection\n");	
	}

	char TxBuffer[1024] = {"Hello from client"};
	char RxBuffer[128] = {};

	SSL_write(ssl, TxBuffer, sizeof(TxBuffer));
	printf("Message sent to the server\n");

	SSL_read(ssl, RxBuffer, sizeof(RxBuffer));
	printf("Received: %s\n", RxBuffer);

	SSL_shutdown(ssl);
	SSL_free(ssl);
	SSL_CTX_free(ctx);


	close(clientSocket);

	return 0;
}
```

