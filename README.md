# Python Netcat Alternative
Netcat is a program that is used to establish a Transmission Control Protocol (TCP) or User Datagram Protocol (UDP) between two devices, usually a client and a server.</br>
The server will be running netcat in listener mode:</br>
`python3 netcat.py -t 127.0.0.1 -p 1234 -l -c`</br>
where `-t` specifies the target, `-p` specifies the port, `-l` specifies listener mode, `-c` specifies command shell.
Once we start listening on port 1234, we can open up another terminal window and enter `nc 127.0.0.1 1234`, which establishes a connection to that port.
From here, we can enter commands such as `whoami`, `ls`, `cat /etc/passwd`, etc. to get information about the listener.

## References
Black Hat Python 2nd Edition
