# glibc-all-in-one

this repo helps you to download & debug glibc easily.

__feature__

- download glibc binary
- download glibc debug file
- download glibc source code
- extract custom glibc
- auto patch

# usage

## download
available command associated with downloading libc:  
1. `update_list` at first.  
2. `remove_duplicate` will delete duplicate libc version info in list.  
3. `download all` will download all libc in list.  
4. `install_build-id` will copy all build-id (debug info) to system debug info dir.  **!!! BE CAREFUL !!!** 

```bash
➜  glibc-all-in-one ./update_list
[+] Common list has been save to "list"
[+] Old-release list has been save to "old_list"

➜  glibc-all-in-one ./remove_duplicate
[+] Removed 0 items in ./list, left: 4
[+] Removed 0 items in ./old_list, left: 0

➜  glibc-all-in-one cat list
2.23-0ubuntu10_amd64
2.23-0ubuntu10_i386
2.23-0ubuntu11_amd64
2.23-0ubuntu11_i386
2.23-0ubuntu3_amd64
2.23-0ubuntu3_i386
2.27-3ubuntu1_amd64
2.27-3ubuntu1_i386
2.28-0ubuntu1_amd64
2.28-0ubuntu1_i386
......

➜  glibc-all-in-one cat old_list
2.21-0ubuntu4.3_amd64
2.21-0ubuntu4.3_amd64
2.21-0ubuntu4_amd64
2.21-0ubuntu4_amd64
2.24-3ubuntu1_amd64
2.24-3ubuntu1_amd64
2.24-3ubuntu2.2_amd64
2.24-3ubuntu2.2_amd64
2.24-9ubuntu2.2_amd64
2.24-9ubuntu2.2_amd64
......

➜  glibc-all-in-one ./download all
Getting 2.31-0ubuntu9.10_amd64
  -> Location: https://mirrors.ustc.edu.cn/ubuntu/pool/main/g/glibc/libc6_2.31-0ubuntu9.10_amd64.deb
  -> Downloading libc binary package
  -> Extracting libc binary package
  -> Package saved to libs/2.31-0ubuntu9.10_amd64
  -> Location: https://mirrors.ustc.edu.cn/ubuntu/pool/main/g/glibc/libc6-dbg_2.31-0ubuntu9.10_amd64.deb
  -> Downloading libc debug package
  -> Extracting libc debug package
  -> Package saved to libs/2.31-0ubuntu9.10_amd64/.debug
  -> Location: https://mirrors.ustc.edu.cn/ubuntu/pool/universe/g/glibc/glibc-source_2.31-0ubuntu9.10_all.deb
  -> Downloading libc source package
  -> Extracting libc source package
  -> Package saved to libs/2.31-0ubuntu9.10_amd64/src
......

➜  glibc-all-in-one ls libs/2.23-0ubuntu10_i386/.debug
ld-2.23.so  libc-2.23.so   ......

➜  glibc-all-in-one ls libs/2.31-0ubuntu9.10_amd64/src
abi-tags        configure  ......
```

## auto patch
`libc-patch` is a script that helps you to patch your ELF file to a specific libc version.  
We call the ELF file waiting for patch `pwn`.  
Given a libc, this script will detect its version and try to find ld in `libs`, using `patchelf` to patch the `pwn`'s libc and ld.  
If no libc path is given, the script will try to search ELF file named like `*libc*` under `pwn`'s dir.  
A backup named `pwn.orig` will be created.  
  
```bash
➜  pwnpwn libc-patch ./pwnpwn
[*] glibc base dir: /usr/lib/glibc
[*] try to find libc under `.`
[*] '/home/wangjihe/pwn/pwnpwn/pwnpwn'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
[*] '/home/wangjihe/pwn/pwnpwn/libc-2.31.so'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
[+] libc: libc.so.6
[+] linker: /lib64/ld-linux-x86-64.so.2
[+] libc version: 2.31-0ubuntu9.9_amd64
[*] replace ld: ['patchelf', '--set-interpreter', '/usr/lib/glibc/2.31-0ubuntu9.9_amd64/ld-linux-x86-64.so.2', './pwnpwn']
[*] replace libc: ['patchelf', '--replace-needed', 'libc.so.6', '/usr/lib/glibc/2.31-0ubuntu9.9_amd64/libc.so.6', './pwnpwn']
continue? (Y/n)
[*] created backup: ./pwnpwn.orig
[*] ld result: 0
[*] libc result: 0
[*] ldd result:
    linux-vdso.so.1 (0x00007ffe0d3ec000)
    /usr/lib/glibc/2.31-0ubuntu9.9_amd64/libc.so.6 (0x00007fa95ac0e000)
    /usr/lib/glibc/2.31-0ubuntu9.9_amd64/ld-linux-x86-64.so.2 => /lib64/ld-linux-x86-64.so.2 (0x00007fa95b1cb000)
```