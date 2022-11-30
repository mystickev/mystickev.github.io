---
layout: default
tags:
- Africahackon
- Memory Forensics
- writeup
---

[back /](/)

# Memory Forensics Writeup

# Vault: Forensics @ AfricaHackon Quals

As my first time playing the Africahackon ctf I would say I really enjoyed
playing the challenges. The Forensics vault was one that took hours of my ctf
time lol, but was worth the time at the end.
This is a writeup of the process I used to solve the challenge.

## Analysis

A file was provided with a .lime extension which from some previous ctf I
learned that such files are the result of extracting memory from a linux system
using the Linux Memory Extractor tool → LiME. Using the command file on the
memory dump shows that it contains data.

Since the file we are working on is a memory dump, the go to tool to analyse it
is Volatility. But for it to analyse the file , it needs to know from what system
the memory dump was fetched. Since we know from Lime that it is obviously a
linux system, we know that running a profile search with volatility would yield
nothing. Unless we know what linux distribution it belongs to which we can
identify by:

```strings dump.lime | grep -i 'Linux version' | uniq```


This command identifies it as Ubuntu 16.04.12, so for our first flag
for the most suitable profile;

```AH{Ubuntu160412}.```

Now we have a distribution and also a kernel version, **Ubuntu16.04** and kernel
version **Linux version 4.15.0-112-generic.**


## Creating an ubuntu1604 volatility profile

We first clone the volatility 2 from the repo;

``` git clone https://github.com.volatilityfoundation/volatility```

```cd volatility/tools/linux ```


Change the kernel detection value in the Makefile to match the linux kernel we
identified, so for our case;

```sed -i ‘s/initial-kernel-value/4.15.0-112-generic/g’ Makefile```

Something to keep in mind while creating volatility 2 profiles, you only need the
Linux headers and a system map. So, goodbye VM and hello docker :).

To setup a container that matches our target os(Ubuntu 16.04);

```$ docker run –it –rm -v $PWD:/volatility ubuntu:16.04 _bin/_ bash```

This will sometimes cause an error that mount point doesn’t exist, A temporary
work around would be;

```$ sudo mkdir /sys/fs/cgroup/systemd && sudo mount -t cgroup -o```

none,name=systemd cgroup /sys/fs/cgroup/systemd

Then run the docker command again;

Install the necessary packages on the container;
`{  
cd volatility 
apt update
apt install build-essential linux-headers-4.15.0-112-generic dwarfdump make
zip linux-image-4.15.0-112-generic
cd /volatility/tools/linux
Create the dwarf file using the volatility tool
make 
}`

Zip the dwarf file and the System Map

``` zip Ubuntu16.04.zip module.dwarf _boot_ /system.map-4.15.0.112-generic```

``` exit ```

``` cp ubuntu1604.zip <volatility-tool-folder>/volatility/plugins/overlays/linux``` 

Now we have a profile, and for the moment of truth,whether it works for us or back to the drawing board lol!

Confirm that the profile is read by volatility

```python2 vol.py --info | grep Linux```

## Solving with volatility

Conduct a file search for flag.odt.ods

```python2 vol.py --profile=LinuxUbuntu1604x64 linux_enumerate_files -f ../capture.lime | grep flag.odt.ods```

Found it! now to dump it and read the flag

```python2 vol.py --profile=LinuxUbuntu1604x64 -f ../capture.lime linux_find_file -i 0xffff9869f49b1a98 -O flag.odt.ods```


```flag → ah {hope_you_built_a_custom_profile}```

### References

Visit the link below for additional information on creating both volatility-2 and volatility-3 profiles.
Using volatility 3 requires symbol tables instead of profiles thus the need for another approach.
https://beguier.eu/nicolas/articles/security-tips-3-volatility-linux-profiles.html


[back /](/)