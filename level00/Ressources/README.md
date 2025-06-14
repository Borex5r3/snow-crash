# 🧊 SnowCrash - Level00 Walkthrough

## Level: 00
Objective: Get the password for user `flag00` and use it to retrieve the flag with `getflag`.

### 🧩 Step-by-step Solution

**1. Explore the system**

Start by looking for any files owned by the user `flag00`, as they might contain useful information:

```bash
find / -user flag00 2>/dev/null
```
✅ Output:

```bash
/usr/sbin/john
```

**2. Inspect the suspicious file**

Check the contents of `/usr/sbin/john`:

```bash
cat /usr/sbin/john
```

✅ Output:

```bash
cdiiddwpgswtgt
```

**3. Analyze the string**
Attempting to use this as a password directly `fails`:

```bash
su flag00
Password: cdiiddwpgswtgt
su: Authentication failure
```

This suggests the string is `encoded`. Let’s try to decode it.

**4. Decoding the string**

After some research or using online tools like dcode.fr, we identify it’s encoded using `Caesar cipher with ROT15` (each letter is shifted by 15 positions in the alphabet).

Decoding `**cdiiddwpgswtgt**` with `ROT15` gives:

```bash
nottoohardhere
```

**5. Try the decoded password**

```bash
su flag00
Password: nottoohardhere
```

✅ Success! Now we’re logged in as `flag00`.

**6. Retrieve the flag**

```bash
getflag
```

✅ Output:

```bash
Check flag.Here is your token : x24ti5gi3x0ol2eh4esiuxias
```

**🔓 Successfully moved to level01!**