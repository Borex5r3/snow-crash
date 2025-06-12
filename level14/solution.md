## 📄 Description

The `getflag` binary is protected by `anti-debugging` checks and a `UID` verification. It exits if run under a debugger or by an unauthorized user. The objective is to bypass these protections using gdb to retrieve the hidden `flag`.

## 🧩 Challenge Overview

- The binary `getflag` detects if it is being debugged using the `ptrace` system call:

```
level14@SnowCrash:~$ gdb getflag
(gdb) disas main
[...]
   0x08048982 <+60>:	movl   $0x0,(%esp)
   0x08048989 <+67>:	call   0x8048540 <ptrace@plt>
   0x0804898e <+72>:	test   %eax,%eax
   0x08048990 <+74>:	jns    0x80489a8 <main+98>
[...]
```

- The binary `getflag` verifies that the executing user’s UID matches a specific privileged user (`flag14`):

```
(gdb) disas main
[...]
   0x08048af5 <+431>:	mov    %eax,(%esp)
   0x08048af8 <+434>:	call   0x80484c0 <fwrite@plt>
   0x08048afd <+439>:	call   0x80484b0 <getuid@plt>
   0x08048b02 <+444>:	mov    %eax,0x18(%esp)
   0x08048b06 <+448>:	mov    0x18(%esp),%eax
[...]
```

- Viewing /etc/passwd showed that the UID of flag14 is 3014, which is required to successfully pass the UID check in the binary:

```
level14@SnowCrash:~$ cat /etc/passwd
[...]
flag14:x:3014:3014::/home/flag/flag14:/bin/bash
```

If either check fails, the program exits without revealing the flag. The binary does not accept input or provide accessible files, making direct exploitation difficult.

## ⚔️ Exploitation Strategy

- Set a breakpoint at address `0x0804898e`, immediately after the `ptrace` call returns:

```
(gdb) b *0x0804898e
Breakpoint 1 at 0x804898e
```

- Set a breakpoint at address `0x08048b02`, which corresponds to a critical point after the anti-debugging checks and before the `UID` verification:

```
(gdb) b *0x08048b02
Breakpoint 2 at 0x8048b02
```

- The program execution stopped at the breakpoint set at `0x0804898e`, right after the `ptrace` call:

```
(gdb) run
Starting program: /bin/getflag

Breakpoint 1, 0x0804898e in main ()
```

- When `ptrace` fails under GDB, it returns `-1`. Setting `EAX` to `0` simulates success and bypasses the anti-debugging check:

```
(gdb) p $eax=0
$1 = 0
```

- Execution paused at `0x08048b02`, just before the `UID` check:

```
(gdb) continue
Continuing.

Breakpoint 2, 0x08048b02 in main ()
```

- The `EAX` register was set to `0x0bc6` (decimal 3014), simulating the UID of the `flag14` user:

```
(gdb) p $eax=0x0bc6
$2 = 3014
```

## ✅ Result

- We `continue` the program’s execution, which now proceeds past the `UID` check and outputs the `flag`:

```
(gdb) continue
Continuing.
Check flag.Here is your token :
[Inferior 1 (process 3606) exited normally]
```

## 🛡️ Mitigation Advice

- **Anti-Debugging Bypass**: `ptrace()` can be easily bypassed.
- **Client-Side Validation**: Critical checks done in user space.
