# TCP-thread-exchange-using-C
String Exchange using TCP connecting to the server with address 127.0.0.1 and port number 5566.

The server-side process can be broken down into the following steps:
socket() – create TCP socket.
bind() – bind the TCP socket to the server address (ip and port).
listen() – waiting for the clients.
accept() – connection is established between the client and server.
recv() and send () – communicate with each other.
close() – close the connection from the client.
Include the required header files.
1
2
3
4
5
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

Define the IP (Internet Protocol) address and port number, that would be used to create a socket. Here, we are using the localhost address for both the server and the client.
 
1
2
char *ip = "127.0.0.1";
int port = 5566;

Here, we define the required variables used later in this program.
1
2
3
4
5
int server_sock, client_sock;
struct sockaddr_in server_addr, client_addr;
socklen_t addr_size;
char buffer[1024];
int n;

Create a TCP (Transmission Control Protocol) socket that returns a socket descriptor. This socket descriptor would be used to communicate with the client.
1
2
3
4
5
6
server_sock = socket(AF_INET, SOCK_STREAM, 0);
if (server_sock < 0){
  perror("[-]Socket error");
  exit(1);
}
printf("[+]TCP server socket created.\n");

Here, we initialize the server address by providing the required IP and port number. The server keeps all the address information for both the server and the client in the sockaddr_in struct.
1
2
3
4
memset(&server_addr, '\0', sizeof(server_addr));
server_addr.sin_family = AF_INET;
server_addr.sin_port = port;
server_addr.sin_addr.s_addr = inet_addr(ip);

Binding the socket descriptor with the server address information.
1
2
3
4
5
6
n = bind(server_sock, (struct sockaddr*)&server_addr, sizeof(server_addr));
if (n < 0){
  perror("[-]Bind error");
  exit(1);
}
printf("[+]Bind to the port number: %d\n", port);

Now, we listen for incoming connections from the clients.
1
2
listen(server_sock, 5);
printf("Listening...\n");

The server handles only one client at a time. So, only one client would communicate and the rest would have to wait for the communication to get completed.
1
2
3
4
5
6
7
8
9
10
11
12
13
14
 
 
while(1){
  addr_size = sizeof(client_addr);
  client_sock = accept(server_sock, (struct sockaddr*)&client_addr, &addr_size);
  printf("[+]Client connected.\n");
 
  bzero(buffer, 1024);
  recv(client_sock, buffer, sizeof(buffer), 0);
  printf("Client: %s\n", buffer);
 
  bzero(buffer, 1024);
  strcpy(buffer, "HI, THIS IS SERVER. HAVE A NICE DAY!!!");
  printf("Server: %s\n", buffer);
  send(client_sock, buffer, strlen(buffer), 0);
 
  close(client_sock);
  printf("[+]Client disconnected.\n\n");
  }

To communicate with all the clients, we start a while loop and the following things happen:
Accept the client connection.
We receive a message from the client and print it on the console.
Next, the server sends a reply message to the client.
Close connection from the client.
 
The client-side process can be broken down into the following steps:
socket() – create TCP socket.
connect() – connect to the server.
recv() and send () – communicate with each other.
close() – close the connection.
We begin by including all the required header files, defining the IP (Internet Protocol) address and the port number. We also define the required variable used later in this program.
1
2
3
4
5
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>

NOTE: Make sure that the IP address and the port number for the client are the same as the server.
1
2
3
4
5
6
7
8
char *ip = "127.0.0.1";
int port = 5566;
 
int sock;
struct sockaddr_in addr;
socklen_t addr_size;
char buffer[1024];
int n;

We start by creating a TCP (Transmission Control Protocol) socket. The socket would be used to connect to the server and start communication.
1
2
3
4
5
6
sock = socket(AF_INET, SOCK_STREAM, 0);
if (sock < 0){
  perror("[-]Socket error");
  exit(1);
}
printf("[+]TCP server socket created.\n");

We provide the required IP address and the port number to the required data structures.
1
2
3
4
memset(&addr, '\0', sizeof(addr));
addr.sin_family = AF_INET;
addr.sin_port = port;
addr.sin_addr.s_addr = inet_addr(ip);

We send a connection request to the server and wait for the server to accept the request.
1
2
connect(sock, (struct sockaddr*)&addr, sizeof(addr));
printf("Connected to the server.\n");

We send a message to the server and wait for the reply.
1
2
3
4
bzero(buffer, 1024);
strcpy(buffer, "HELLO, THIS IS CLIENT.");
printf("Client: %s\n", buffer);
send(sock, buffer, strlen(buffer), 0);

We receive the reply from the server and print it on the console.
1
2
3
bzero(buffer, 1024);
recv(sock, buffer, sizeof(buffer), 0);
printf("Server: %s\n", buffer);

At last, we close the connection from the server.
1
2
close(sock);
printf("Disconnected from the server.\n");

____________________________________________________________________________
