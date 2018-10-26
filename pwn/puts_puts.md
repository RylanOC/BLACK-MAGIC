# **Puts Puts**

## **Create Your Own libc Leaks**

Sometimes we can abuse function pointers to get a libc leak, but often times doing so is infeasible. However, if we have RIP control we can force a leak through a ROP chain calling `puts(puts)`

This might seem like a catch-22 at first, since we need an address for puts in order to call it from our ROP chain. Luckily, ELFs have two special regions which we can leverage to build our gadgets:



PLT - Procedure Linkage Table

- Contains code stubs for every libc function used in your code

  - Code stubs will look up the appropriate address in libc
  - Once the address is known, the stub will patch itself to jump directly to it 

- The PLT is usually patched at load time to jump to the correct address

- Accessible in pwntools via 

  ```python
  elf.plt['function_name']
  ```



GOT - Global Offset Table

- Same principle as the PLT
  - The main difference (that we care about at least) is that the GOT contains pointers, while the PLT contains jmp statements
- Accessible in pwntools via

```python
  elf.got['function_name']
```



Now that we know how to call the appropriate functions using the PLT as well as how to retrieve its address from the GOT, we can finally construct our ROP chain to call `puts@PLT(puts@GOT)`.

Example pwntools script to find leak:

```python
from pwn import *

# tell pwntools about your system so it knows calling convention, etc.
context(arch='x86', os='linux', endian='little')

# create objects to help us parse the elf and create ROP chains
elf = ELF('path/to/binary')
rop = ROP(elf)
p = process('path/to/binary')

# get a ROP gadget to call puts with a pointer to an address in libc as an argument
rop.puts(elf.got['puts'])
p.sendline('A'*24+str(rop))
```



## Additional Resources

[More Detailed Look at PLT/GOT for Pwning Purposes](https://systemoverlord.com/2017/03/19/got-and-plt-for-pwning.html)

[Example Writeup Using Puts Puts with Pwntools](http://ctfhacker.com/ctf/pwnable/2015/08/18/campctf-bitterman.html)