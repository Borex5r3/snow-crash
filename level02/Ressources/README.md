# 🧊 SnowCrash - Level02 Walkthrough

## Level: 02

**Objective:** Extract the password for user `flag02` from a `.pcap` file and retrieve the flag using `getflag`.

## 🧩 Step-by-step Solution

**1. Locate the file**

Upon logging in, we find a suspicious network capture file:

```bash
ls
level02.pcap
```

**2. Copy the file to local machine for analysis**

Use scp to securely copy the file to your local host:

```bash
scp -P 4242 level02@10.12.180.102:~/level02.pcap .
```

**3. Analyze the packet capture**

Use `tshark` (command-line version of Wireshark) to extract the raw packet data:

```bash
tshark -Tfields -e data -r level02.pcap | tr -d '\n' > data
```

**4.Inspect the extracted data**

```bash
cat data
```

The file contains a long hex string of raw data.

**5. Decode the hex string**

Use Python to decode the hex string:

```bash
python3
>>> hex_data = "fffd25fffc25fffb26...62756773206c6f67696e3a20"
>>> bytes.fromhex(hex_data).decode('latin1')
```

✅ Output contains:

```bash
[...]Password: ft_wandr\x7f\x7f\x7fNDRel\x7fL0L\r[...]
```

**6. Clean the extracted string**

After manually removing non-printable characters (like `\x7f`), the result is:

```bash
ft_waNDReL0L
```

**7. Switch to the target user**

```bash
su flag02
Password: ft_waNDReL0L
```

✅ Success! Now logged in as `flag02`.

**8. Retrieve the flag**

```bash
getflag
```

✅ Output:

```bash
Check flag. Here is your token : kooda2puivaav1idi4f57q8iq
```

**🔓 Successfully moved to level03!**