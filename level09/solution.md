## 📄 Description

In this level, we get a SetUID binary and a token file that’s encrypted by adding each byte’s index to its value. The goal is to decrypt the token by subtracting the index from each byte to recover the real password, then use it to get the flag.

## 🧩 Challenge Overview

- Binary: level09 (SetUID, owned by flag09)

```
-rwsr-sr-x 1 flag09 level09 7.5K Mar  5  2016 level09
```

- Token file (readable but obfuscated):

```
-r--r--r-- 1 flag09 level09 26 Mar 5 2016 token
```

- Example token content (hexdump):

```
00000000  66 34 6b 6d 6d 36 70 7c 3d 82 7f 70 82 6e 83 82  |f4kmm6p|=..p.n..|
00000010  44 42 83 44 75 7b 7f 8c 89 0a                    |DB.Du{....|
```

## ⚔️ Exploitation Strategy

1. Extract the encrypted token bytes from the file and the binary’s embedded data.
2. Use the decryption script (decrypt_token.py) provided in this directory to subtract each byte’s index and recover the password.
3. Use the decrypted password to switch user to `flag09` and run `getflag`.

## ✅ Result

- The decrypted password looks like:

```
f3iji1ju5yuevaus41q1afiuq
```

- Use this password to login as flag09:

```
level09@SnowCrash:~$ su flag09
Password:
Don't forget to launch getflag !
flag09@SnowCrash:~$ getflag
Check flag.Here is your token :
```

## 🛡️ Mitigation Advice (Real Systems)

This challenge shows a simple obfuscation that is easy to reverse. To avoid this in real applications:

- Use strong encryption or cryptographic hashes for secrets instead of reversible obfuscation.
- Restrict token file permissions strictly.
