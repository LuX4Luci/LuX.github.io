## <span style="color:red">Don't Panic it's a step by step tutorial :</span>

- ### `First of all as good step happed in my way in Cyber Security I Have learned How to use Linux distributions as it's my main Operating System So I will show you how easy to install kali linux with VirtualBox`

> First Open your Browser And Google this like Picturs :
![](/img/03/01.png)
Then choose [www.kali.org](https://www.kali.org){:target="_blank"}
>>![](/img/03/02.png)
Then Scroll down to Bare Metal then Boom Download.
>>>![](/img/03/03.png)

- Second Start Googling About VirtualBox :
>![](/img/03/04.png)
>Then Choose Download Link: [www.virtualbox.org](https://www.virtualbox.org/wiki/Downloads){:target="_blank"}
![](/img/03/05.png)
- After choosing which OS you have and setup it Then OPEN IT 
![](/img/03/06.png)

- Then :

> ![](/img/03/07.png)

- I recommend Choose as minimal choice about 2 GB of memory to 4 GB :

> ![](/img/03/08.png)

- Then Create Virtual hard disk :

> ![](/img/03/09.png)

- To know [what is the difference between VDI , VHD and VMDK](https://www.parallels.com/blogs/ras/vdi-vs-vhd-vs-vmdk/){:target="_blank"} For now Choose VDI :

> ![](/img/03/10.png)

- I would prefer dynamically allocated choice instead of fixed size because it won't fill all the wanted space immediately but when you use it .

> ![](/img/03/11.png)

- For long working space i recommend about 100 GB space for the VM :

> ![](/img/03/12.png) 

- Now Start The machine then choose the ISO file we downloaded at the start :

> ![](/img/03/13.png)

> ![](/img/03/14.png)

- Now Choose The Graphical install (GUI) :

> ![](/img/03/15.png)

# installation steps :

1. Select Language :
> ![](/img/03/16.png)
  
2. Select your location :
> ![](/img/03/17.png)

3. Configure your KeyBoard : 
> ![](/img/03/18.png)

4. Configure the network
> ![](/img/03/19.png)
> ## choose your hostname 
> ![](/img/03/20.png)

5. As normal you don't have a domain so leave it empty :
> ![](/img/03/21.png)

6. Now setup the root account :
- If you set a password for the root account it should be so strong password (DON'T FORGET IT) , and if you didn't set a password for the root account it will be disabled and the system's initial user account will be given the power to become root using `sudo` command.

> ![](/img/03/22.png) 

# Partitioning Step :

I will explain it Okay ? Just focus and do more search 😉️ .
 
- Choose the hard disk and follow me .

> ![](/img/03/23.png)
> ![](/img/03/24.png)

- Now Choose the continuous free space then continue :

> ![](/img/03/25.png)

- Create New partition :

> ![](/img/03/26.png)

- We will make `1 GB` For the Boot partition at the beginning :

> ![](/img/03/27.png)
> ![](/img/03/28.png)
> ![](/img/03/29.png)

- Then at the mount point we will choose from the list `/boot` then Done setting up the partition :

> ![](/img/03/30.png)
> ![](/img/03/31.png)

- Choose the next free space :

> ![](/img/03/32.png)

- ### And choose a 2GB and it will be our swap area <span style="color:red">Don't make it more than the half of your RAM Capcity or equal to it , Making it bigger size may harm your hard disk </span> _(Search About Swap area well)_

> ![](/img/03/33.png)
> ![](/img/03/34.png)
- At the Use As point Choose `SWAP AREA` :
>> ![](/img/03/35.png)
>> ![](/img/03/36.png)
>> ![](/img/03/37.png)

- The Next Free Space We Will Use It All For our Root Directory `/` :
- Choose from the Mount Point : >  **_/_**   <
- Then finish every thing and write changes to disk like the pictures
> ![](/img/03/38.png)
> ![](/img/03/39.png)
> ![](/img/03/40.png)
> ![](/img/03/41.png)

- ### Installing system stage will take aboud 30 minutes less or more depending on your hard disk (read and write speed and if it's SSD or HDD) 

> ![](/img/03/42.png)


- If you use an old release you can choose `Yes` at Use a Network mirror ?

> ![](/img/03/43.png)

- Configuring the package manager : (We Will Leave It Blank)

> ![](/img/03/44.png)
> ![](/img/03/45.png)

- The GRUB Boot loader on hard disk :
**GRUB boot loader is the main loader for Linux at start up as example :  when there are many operating system installed in hard disk the loader of linux is the GRUB that's tell bios Hey I'm over here start Linux UP 😂️**

> ![](/img/03/46.png)

- When choosing Device for boot loader Choose your hard disk you see at the patitioning stage beside the free space you will see `/dev/sda` and the number beside every partition will be like this `/dev/sda1` or any number 

> ![](/img/03/47.png)

- ## Now Finishing Installation :

> ![](/img/03/48.png)
> ![](/img/03/49.png)
> ![](/img/03/50.png)

- ## It will start booting like this : 

> ![](/img/03/51.png)

- Now write your **credentials** (_Username_, _Password_) :

> ![](/img/03/52.png)

- # And BoOoM it's installed :

> ![](/img/03/53.png)

-  root@kali:~# init 0                   |(For Shutting Down Machine) 
