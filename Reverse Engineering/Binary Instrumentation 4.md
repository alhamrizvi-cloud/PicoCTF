Instead of going through heavy debugging, you can take a much cleaner route.

First, run **binwalk** on the executable. It will automatically scan for embedded data and extract anything hidden inside. After extraction, you’ll notice a file named **`6000`** gets created.

Now, take that `6000` file and load it into **Ghidra**. Once inside Ghidra, analyze it like a normal binary—check the functions, look at strings, and follow the logic to understand what it’s doing.

Alright, I’ll clean this up and walk you through it in a simple “Caprio style” flow.

---

## 🧾 Cleaned (readable) version of the code

```c
int main() {
    char *server_ip = "192.168.29.25";
    int port = 9875; // 0x268b

    string correct_key = "key68555664";
    string prompt = "Enter the key:";

    SOCKET sock;
    WSADATA wsa;
    char buffer[512];

    // Initialize Winsock
    if (WSAStartup(0x202, &wsa) != 0) {
        cout << "WSAStartup failed.\n";
        return 1;
    }

    // Create socket
    sock = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    if (sock == -1) {
        cout << "Socket creation failed.\n";
        WSACleanup();
        return 1;
    }

    // Setup server address
    sockaddr addr;
    addr.sa_family = AF_INET;
    *((short*)addr.sa_data) = htons(port);
    inet_pton(AF_INET, server_ip, addr.sa_data + 2);

    // Connect to server
    if (connect(sock, &addr, sizeof(addr)) == -1) {
        cout << "Connection failed.\n";
        closesocket(sock);
        WSACleanup();
        return 1;
    }

    // Send prompt
    send(sock, prompt.c_str(), prompt.length(), 0);

    // Receive response
    int len = recv(sock, buffer, 511, 0);
    if (len < 1) {
        cout << "No response from server.\n";
        closesocket(sock);
        WSACleanup();
        return 1;
    }

    buffer[len - 1] = '\0';
    string received = buffer;

    // Compare key
    if (received == correct_key) {
        string message = "Congratulations! Here's your flag:\n";

        // Append flag parts
        for (each part in flagParts) {
            message += part;
        }

        send(sock, message.c_str(), message.length(), 0);
    } else {
        string message = "Wrong key";
        send(sock, message.c_str(), message.length(), 0);
    }

    closesocket(sock);
    WSACleanup();
    return 0;
}
```

This program is basically acting like a **client that talks to a server**.

* IP: `192.168.29.25`
* Port: `9875`
* Correct key: `"key68555664"`

So already you see something important

### 2. It connects to a server

* Starts Windows socket (`WSAStartup`)
* Creates a socket
* Connects to that IP + port

So this binary is not standalone — it depends on a server.

### 3. Sends a message

It sends:

Enter the key:

### 4. Receives response

Server sends something back → stored in `buffer`

### 5. Key check happens HERE

```c
if (received == "key68555664")
```

👉 That means:

* The **server must send the correct key**
* Not the user typing locally
* This is important: **validation is done client-side after receiving data**


### 6. If correct key

* It builds the flag like this:

```c
message = "Congratulations! Here's your flag:\n";
message += flagParts[0];
message += flagParts[1];
...
```

👉 Flag is stored in pieces (`flagParts`)
👉 Then concatenated


### 7. If wrong key

It sends:

```
Wrong key
```

now on program tree, click .rdata

<img width="210" height="176" alt="image" src="https://github.com/user-attachments/assets/5ef5f586-1937-4524-b8b1-6fbab9c630fd" />

<img width="1416" height="743" alt="image" src="https://github.com/user-attachments/assets/1194060f-759e-4779-90d3-cefac0251684" />

<img width="1416" height="743" alt="image" src="https://github.com/user-attachments/assets/f2813a60-b50a-41c4-b883-cde8ca1bc4c1" />

decode these base64 strings :)

