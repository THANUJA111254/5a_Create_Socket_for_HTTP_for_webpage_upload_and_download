# 5a_Create_Socket_for_HTTP_for_webpage_upload_and_download
## AIM :
To write a PYTHON program for socket for HTTP for web page upload and download
## Algorithm

1.Start the program.
<BR>
2.Get the frame size from the user
<BR>
3.To create the frame based on the user request.
<BR>
4.To send frames to server from the client side.
<BR>
5.If your frames reach the server it will send ACK signal to client otherwise it will send NACK signal to client.
<BR>
6.Stop the program
<BR>
## Program :
## SERVER:
```
import socket

def start_server(host='localhost', port=8080):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server:
        server.bind((host, port))
        server.listen(1)
        print(f"Server running on {host}:{port}")
        
        while True:
            conn, addr = server.accept()
            with conn:
                print(f"Connected by {addr}")
                data = conn.recv(4096).decode()
                if not data:
                    break

                # Save uploaded file content (after headers)
                if "POST /upload" in data:
                    content = data.split('\r\n\r\n', 1)[1]
                    with open("received.txt", "w") as f:
                        f.write(content)
                    conn.sendall(b"HTTP/1.1 200 OK\r\n\r\nFile uploaded successfully.")

                elif "GET /" in data:
                    filename = data.split("GET /", 1)[1].split(" ", 1)[0]
                    try:
                        with open(filename, "r") as f:
                            content = f.read()
                        response = f"HTTP/1.1 200 OK\r\n\r\n{content}"
                    except FileNotFoundError:
                        response = "HTTP/1.1 404 Not Found\r\n\r\nFile not found."
                    conn.sendall(response.encode())

if __name__ == "__main__":
    start_server()
```
## CLIENT:
```
import socket

def send_request(host, port, request):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        s.sendall(request.encode())
        response = s.recv(4096).decode()
    return response

def upload_file(host, port, filename):
    with open(filename, 'r') as file:
        file_data = file.read()
        content_length = len(file_data)
        request = f"POST /upload HTTP/1.1\r\nHost: {host}\r\nContent-Length: {content_length}\r\n\r\n"
        request += file_data
        response = send_request(host, port, request)
    return response

def download_file(host, port, filename):
    request = f"GET /{filename} HTTP/1.1\r\nHost: {host}\r\n\r\n"
    response = send_request(host, port, request)
    file_content = response.split('\r\n\r\n', 1)[1]
    with open("downloaded_" + filename, 'w') as file:
        file.write(file_content)

if __name__ == "__main__":
    host = 'localhost'
    port = 8080

    
    upload_response = upload_file(host, port, 'example.txt')
    print("Upload response:", upload_response)


    download_file(host, port, 'received.txt')
    print("File downloaded successfully.")
```


## OUTPUT:
<img width="873" height="394" alt="image" src="https://github.com/user-attachments/assets/5231980e-102f-47f7-84eb-21dbc2f4a70c" />

<img width="1029" height="403" alt="image" src="https://github.com/user-attachments/assets/291e9c4c-6ad2-4e31-8307-057bbeedcf89" />



## Result
Thus the socket for HTTP for web page upload and download created and Executed
