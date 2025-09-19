🕒 TCP Time Client in C (NIST Daytime Protocol)
This C program connects to the NIST Internet Time Service using TCP on port 13 (Daytime Protocol). It retrieves the current time in a human-readable format.

✅ Why this works
NIST time servers (e.g., 129.6.15.28) respond with a simple ASCII string containing the current UTC time.

Port 13 is used for the Daytime Protocol, which is designed for lightweight time queries.

This is ideal for educational demos, PD workshops, or classroom projects where you want to show real-time data without complex parsing.

📦 Requirements
GCC or any C compiler

Internet access (port 13 must be open)

Linux/macOS or WSL (for Windows)

📄 Full Code: nist_time_client.c
c
#include <stdio.h>              // Standard I/O functions
#include <stdlib.h>             // General utilities (exit, etc.)
#include <string.h>             // String manipulation
#include <unistd.h>             // POSIX API (read, close)
#include <arpa/inet.h>          // Internet address manipulation

#define SERVER_IP "129.6.15.28" // NIST time server (time.nist.gov)
#define SERVER_PORT 13          // Daytime Protocol port
🔍 Explanation:
Includes necessary headers for socket programming.

Defines the IP and port of the NIST server.

c
int main() {
    int sock;
    struct sockaddr_in server_addr;
    char buffer[1024] = {0};
🔍 Explanation:
sock: file descriptor for the TCP socket.

server_addr: structure to hold server address info.

buffer: stores the incoming time string.

c
    sock = socket(AF_INET, SOCK_STREAM, 0);
    if (sock < 0) {
        perror("Socket creation failed");
        return 1;
    }
🔍 Explanation:
Creates a TCP socket (SOCK_STREAM) using IPv4 (AF_INET).

Checks for errors in socket creation.

c
    server_addr.sin_family = AF_INET;
    server_addr.sin_port = htons(SERVER_PORT);
    if (inet_pton(AF_INET, SERVER_IP, &server_addr.sin_addr) <= 0) {
        perror("Invalid address");
        return 1;
    }
🔍 Explanation:
Sets up the server address structure.

Converts the IP string to binary format using inet_pton.

c
    if (connect(sock, (struct sockaddr *)&server_addr, sizeof(server_addr)) < 0) {
        perror("Connection failed");
        return 1;
    }
🔍 Explanation:
Attempts to connect to the NIST server.

If it fails, prints an error and exits.

c
    int bytes_read = read(sock, buffer, sizeof(buffer) - 1);
    if (bytes_read > 0) {
        buffer[bytes_read] = '\0';
        printf("Current time from NIST: %s\n", buffer);
    } else {
        printf("No data received.\n");
    }
🔍 Explanation:
Reads the response from the server.

Ensures the buffer is null-terminated.

Prints the received time string.

c
    close(sock);
    return 0;
}
🔍 Explanation:
Closes the socket and exits cleanly.

🛠️ Notes
🔐 Firewall warning: Port 13 may be blocked on some networks (especially school or enterprise firewalls).

🌐 Server reliability: NIST servers may rate-limit or reject frequent queries. Use responsibly.

🇹🇭 Localization: The time string is in UTC. You can convert it to Thai local time using C libraries like time.h if needed.
