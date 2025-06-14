## 📄 Description

The `level13` binary checks if the current UID is `4242` and exits if it’s not. Using `gdb`, I set a breakpoint after the `getuid()` call and modified the `%eax` register (which holds the UID) to `0x1092` (4242 in hex). This bypasses the UID check and allows the program to reveal the token.

## 🧩 Challenge Overview

The objective of this challenge is to retrieve the access token belonging to the `flag13` user.

- The provided binary (level13) is a `SETUID` executable, owned by `flag13`, with the following permissions:

```
-rwsr-sr-x 1 flag13  level13 7.2K Aug 30  2015 level13
```

- The script performs a `UID` check to ensure it is executed by a user with UID `4242`:

```
level13@SnowCrash:~$ ./level13
UID 2013 started us but we we expect 4242
```

- Using `gdb`, we disassembled the `main` function and identified a key check where the program compares the result of `getuid()` with `0x1092` (4242 in decimal):

```
level13@SnowCrash:~$ gdb ./level13
(gdb) disas main
Dump of assembler code for function main:
[...]
   0x08048595 <+9>:	call   0x8048380 <getuid@plt>
   0x0804859a <+14>:	cmp    $0x1092,%eax
   0x0804859f <+19>:	je     0x80485cb <main+63>
[...]
```

## ⚔️ Exploitation Strategy

- We set a breakpoint at address `0x0804859a`, which is the instruction immediately following the `getuid()` call:

```
(gdb) break *0x0804859a
Breakpoint 1 at 0x804859a
```

- When running the program in `gdb`, execution stopped at the breakpoint set at address `0x0804859a`:

```
(gdb) run
Starting program: /home/user/level13/level13

Breakpoint 1, 0x0804859a in main ()
```

- we manually set the value of the `eax` register to `0x1092` (decimal `4242`) using the command:

```
(gdb) set $eax = 0x1092
```

## ✅ Result

- We `continue` the program’s execution, which now proceeds past the `UID` check and outputs the `flag`:

```
(gdb) continue
Continuing.
your token is
[Inferior 1 (process 2589) exited with code 050]
```

## 🛡️ Mitigation Advice

This challenge exploits a vulnerability known as `Runtime UID Check Bypass`, a form of `Privilege Escalation` caused by `insecure assumptions about the runtime environment`.

To mitigate such vulnerabilities, it is important to:

- Avoid relying **solely** on `UID` comparisons for authentication.
- Implement stronger `verification mechanisms`.
