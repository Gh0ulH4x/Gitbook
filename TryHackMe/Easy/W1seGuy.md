
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._
jpg#XOR_encryption
First start nmap and look for open ports on the sever 
```
nmap -A -p- 10.10.160.37
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-21 04:22 EDT
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
1337/tcp open  waste?

```
which we checked and find open port "1337" and then we used netcat to listen on port 
```
nc 10.10.160.37 1337  

This XOR encoded text has flag 1: 6c231d0101090a3c14057d13243b054c5f331112790522491054272912244a1f294a044a131f080c
What is the encryption key?

```
then we got the XOR encrypted key then we looked at the source code provided into the ctf challenge then  we find out about the server working then use build a script to run and get the flag 
```New.py 
def find_xor_key_and_decode(encoded_text, known_start, known_end, key_length=5):
    # Convert the encoded text to a list of bytes
    encoded_bytes = bytes.fromhex(encoded_text)
    
    # Convert known letters to their byte representation
    known_start_bytes = known_start.encode()
    known_end_byte = known_end.encode()
    
    # Find the first part of the key by XORing known start bytes with the corresponding encoded bytes
    key_start = bytes([encoded_bytes[i] ^ known_start_bytes[i] for i in range(len(known_start_bytes))])
    
    # Find the last part of the key by XORing the known end byte with the last byte of the encoded text
    key_end = encoded_bytes[-1] ^ known_end_byte[0]
    
    # Assuming the key is repeating and its length is key_length
    key = key_start + bytes([key_end])
    
    # Ensure the key length is exactly key_length
    key = key[:key_length]
    
    # Decode the entire message using the key
    decoded_message = bytes([encoded_bytes[i] ^ key[i % key_length] for i in range(len(encoded_bytes))]).decode('latin1')
    
    return key, decoded_message

# Example usage
encoded_text = input("Enter the encoded text in hexadecimal: ")
known_start = input("Enter the first 4 known letters: ")
known_end = input("Enter the last known letter: ")

key, decoded_message = find_xor_key_and_decode(encoded_text, known_start, known_end)
print("The XOR key is:", key.decode('latin1'))  # Use 'latin1' to safely decode bytes to string
print("The decoded message is:", decoded_message)

```
and this work fine and it gives result 
```pytho/n new.py
Enter the encoded text in hexadecimal: 302d3729475504163c43211d0e13431051193954250b0861560829033a621611036242161d35204a
Enter the first 4 known letters: [flag omitted]
The XOR key is: dezR7
The decoded message is: [flag omitted] 
```

and then server responded to this script like 
```
nc 10.10.160.37 1337

This XOR encoded text has flag 1: 302d3729475504163c43211d0e13431051193954250b0861560829033a621611036242161d35204a
What is the encryption key? dezR7
Congrats! That is the correct key! Here is flag 2: [flag omitted]
```
Got the Flag
```Flag
[flag omitted]
```