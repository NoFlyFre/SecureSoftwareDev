This level looks at the concept of modifying variables to specific values in the program, and how the variables are laid out in memory.

This level is at /opt/protostar/bin/stack1

Hints

If you are unfamiliar with the hexadecimal being displayed, “man ascii” is your friend.
Protostar is little endian
Source code
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main(int argc, char **argv)
{
  volatile int modified;
  char buffer[64];

  if(argc == 1) {
      errx(1, "please specify an argument\n");
  }

  modified = 0;
  strcpy(buffer, argv[1]);

  if(modified == 0x61626364) {
      printf("you have correctly got the variable to the right value\n");
  } else {
      printf("Try again, you got 0x%08x\n", modified);
  }
}

user@protostar:/opt/protostar/bin$ gdb ./stack1
GNU gdb (GDB) 7.0.1-debian
Copyright (C) 2009 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "i486-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /opt/protostar/bin/stack1...done.
(gdb) disass main
Dump of assembler code for function main:
0x08048464 <main+0>:    push   %ebp
0x08048465 <main+1>:    mov    %esp,%ebp
0x08048467 <main+3>:    and    $0xfffffff0,%esp
0x0804846a <main+6>:    sub    $0x60,%esp
0x0804846d <main+9>:    cmpl   $0x1,0x8(%ebp)
0x08048471 <main+13>:   jne    0x8048487 <main+35>
0x08048473 <main+15>:   movl   $0x80485a0,0x4(%esp)
0x0804847b <main+23>:   movl   $0x1,(%esp)
0x08048482 <main+30>:   call   0x8048388 <errx@plt>
0x08048487 <main+35>:   movl   $0x0,0x5c(%esp)
0x0804848f <main+43>:   mov    0xc(%ebp),%eax
0x08048492 <main+46>:   add    $0x4,%eax
0x08048495 <main+49>:   mov    (%eax),%eax
0x08048497 <main+51>:   mov    %eax,0x4(%esp)
0x0804849b <main+55>:   lea    0x1c(%esp),%eax
0x0804849f <main+59>:   mov    %eax,(%esp)
0x080484a2 <main+62>:   call   0x8048368 <strcpy@plt>
0x080484a7 <main+67>:   mov    0x5c(%esp),%eax
0x080484ab <main+71>:   cmp    $0x61626364,%eax
0x080484b0 <main+76>:   jne    0x80484c0 <main+92>
0x080484b2 <main+78>:   movl   $0x80485bc,(%esp)
0x080484b9 <main+85>:   call   0x8048398 <puts@plt>
0x080484be <main+90>:   jmp    0x80484d5 <main+113>
0x080484c0 <main+92>:   mov    0x5c(%esp),%edx
0x080484c4 <main+96>:   mov    $0x80485f3,%eax
0x080484c9 <main+101>:  mov    %edx,0x4(%esp)
0x080484cd <main+105>:  mov    %eax,(%esp)
0x080484d0 <main+108>:  call   0x8048378 <printf@plt>
0x080484d5 <main+113>:  leave  
0x080484d6 <main+114>:  ret    
End of assembler dump.
(gdb) 


