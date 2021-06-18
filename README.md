# Python Netcat
Netcat is the "swiss-army knife" of networking utilities. Notably, it can be used to establish a Transmission Control Protocol (TCP) or User Datagram Protocol (UDP) between two devices, usually a client and a server.</br></br>
Reimplementing netcat featues in python will not only give us a deeper understanding of some of these netcat features, but there may also arise some fringe case where a computer does not have netcat but it has python, and so we can run this program through a command line.

## Usage
The server will be running netcat in listener mode:</br>
`python3 netcat.py -t 127.0.0.1 -p 1234 -l -c`</br>
where `-t` specifies the target, `-p` specifies the port, `-l` specifies listener mode, `-c` specifies command shell.
Once we start listening on port 1234, we can open up another terminal window and enter `nc 127.0.0.1 1234`, which establishes a connection to that port.
From here, we can enter commands such as `whoami`, `ls`, `cat /etc/passwd`, etc. to get information about the listener.

## How it Works
- The `execute` function takes in a command as a parameter, runs it, and returns the output as a string. The check_output method runs a command on the local OS and then returns the output from that command.
```
def execute(cmd):
    cmd = cmd.strip()
    if not cmd:
        return
    output = subprocess.check_output(shlex.split(cmd,), stderr=subprocess.STDOUT)

    return output.decode()
```
Main Block:
- Responsible for handling the command-line arguments and calling the rest of the functions
- The argparse module used to create a command line interface
- We can provide it arguments such that it can be invoked to upload a file, execute a command, or start a command shell
```
if __name__ == '__main__':
    parser = argparse.ArgumentParser(
        description='BHP Net Tool',
        formatter_class=argparse.RawDescriptionHelpFormatter,
```
- Example usage, as shown below, is provided that the program will display when the user invokes it with `--help`
```
        epilog=textwrap.dedent('''Example:
            netcat.py -t 192.168.1.108 -p 5555 -l -c # command shell
            netcat.py -t 192.168.1.108 -p 5555 -l -u=mytest.txt # upload to file
            netcat.py -t 192.168.1.108 -p 5555 -l -e=\"cat /etc/passwd\" # execute command
            echo 'ABC' | ./netcat.py -t 192.168.1.108 -p 135 # echo text to server port 135
            netcat.py -t 192.168.1.108 -p 5555 # connect to server
        '''))
```
- 6 arguments can be added to specify how we want the program to behave:
  - `-c` sets up an interactive shell
  - `-e` executes one specific command
  - `-l` indicates that a listener should be set up
  - `-p` specifies the port on which to communicate
  - `-t` specifies the target IP
  - `-u` specifies the name of a file to upload
- `-c`, `-e`, `-u` arguments imply the `-l` argument, because those arguments apply only to the listener side of the communication
- The sender side makes the connection to the listener, so it only needs the `-t` and `-p` arguments to define the target listener
```
    parser.add_argument('-c', '--command', action='store_true', help='command shell')
    parser.add_argument('-e', '--execute', help='execute specified command')
    parser.add_argument('-l', '--listen', action='store_true', help='listen')
    parser.add_argument('-p', '--port', type=int, default=5555, help='specified port')
    parser.add_argument('-t', '--target', default='192.168.1.203', help='specified IP')
    parser.add_argument('-u', '--upload', help='upload file')
    args = parser.parse_args()
```
- If we choose to set it up as listener, the `NetCat` object is invoked with an empty buffer string. Otherwise, the buffer content is sent from `stdin`
- The `run` method is then called to start it up
```
    if args.listen:
        buffer = ''
    else:
        buffer = sys.stdin.read()

    nc = NetCat(args, buffer.encode())
    nc.run()
```



## References
Black Hat Python 2nd Edition
