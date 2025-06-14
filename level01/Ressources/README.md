# 🧊 SnowCrash - Level01 Walkthrough

## Level: 01

**Objective:** Get the password for user `flag01` and use it to retrieve the flag with `getflag`.

### 🧩 Step-by-step Solution

**1. Inspect the `/etc/passwd` file**

The `/etc/passwd` file contains the hashed password for `flag01`:

```bash
cat /etc/passwd
```

✅ Output:

```bash
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
```

**2. Try using the hash as a password**

```bash
su flag01
Password: 42hDRfypTqqnw
```

❌ Authentication failed — the string is not the raw password, it’s a hash.

**3. Crack the password using John the Ripper**

We’ll use John the Ripper, a popular password cracker.

- Save the hash to a file:

```bash
echo "42hDRfypTqqnw" > hash.txt
```

- Run John:

```bash
john hash.txt
```

✅ Output:

```bash
abcdefg      
```

**4. Try the cracked password**

```bash
su flag01
Password: abcdefg
```

✅ Success! Now we’re logged in as flag01.

**5. Retrieve the flag**

```bash
getflag
```

✅ Output:

```bash
Check flag.Here is your token : f2av5il02puano7naaf6adaaf
```

**🔓 Successfully moved to level02!**