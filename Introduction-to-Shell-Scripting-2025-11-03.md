# Introduction to Shell Scripting

**Class date:** Monday, 3 November 2025  
**Topic:** Shell scripting and Linux basics — automation, variables, conditionals, loops, permissions, text tools, and functions

---

## 1. Introduction to Shell Scripting

### 1.1 What Is Shell Scripting?

**Shell scripting** is writing sequences of commands for a **shell** (a command-line interpreter) to run. It is one of the main ways to **automate tasks** on UNIX and Linux: backups, deployments, log processing, and system administration. Shell scripts are plain-text programs executed by the shell (commonly **bash**).

### 1.2 Creating a Shell Script

**Steps:**

1. Create a new file with a `.sh` extension, e.g. `hello_world.sh`.
2. Start the script with a **shebang** so the system knows which interpreter to use.
3. Add commands and shell syntax below.

**Shebang:**

- **`#!/bin/bash`** — Direct path to bash. Works on most Linux systems.
- **`#!/usr/bin/env bash`** — Often preferred for **portability**: `env` looks up `bash` in `PATH`, so the script works even if bash is installed in a different location (e.g. macOS, some containers).
- Avoid `#!/bin/sh` unless you need **POSIX** compatibility; `sh` is not bash and has fewer features.

**Example:**

```bash
#!/usr/bin/env bash
echo "Hello, World!"
```

**Best practices:** Make the script executable with `chmod +x`; use consistent style; consider running scripts through **ShellCheck** to catch common errors.

### 1.3 Script Execution

| Step | Command / action |
|------|-------------------|
| Make executable | `chmod +x filename.sh` |
| Run (from current directory) | `./filename.sh` |
| Run with bash explicitly | `bash filename.sh` (works even without execute bit) |

Using `./` runs the script in the current directory; the shell uses the shebang to choose the interpreter.

### 1.4 Variables and Data

**Defining variables:**

- Syntax: `variable_name=value` (no spaces around `=`).
- By convention, **uppercase** is used for variable names (e.g. `USERNAME="JohnDoe"`).
- Access a variable with **`$VARIABLE_NAME`** (or `${VARIABLE_NAME}` for clarity when concatenating).

**Quoting:** Quote variables when they may contain spaces or special characters (e.g. `"$VAR"`). Always quote variables that come from user input or command arguments to avoid word-splitting and globbing.

**Reading input:**

```bash
read -p "Enter your name: " USERNAME
```

`-p` prints a prompt; the value is stored in `USERNAME`.

**Example:**

```bash
#!/usr/bin/env bash
USERNAME="JohnDoe"
echo "Hello, $USERNAME"
read -p "Enter value: " INPUT
echo "You entered: $INPUT"
```

---

## 2. Conditionals and Loops

### 2.1 The `if` Statement

**Conditional execution** uses `if` with a **condition** inside `[ ]` (or `[[ ]]` in bash for safer quoting and extra operators).

**Syntax:**

```bash
if [ condition ]; then
  commands
elif [ condition ]; then
  commands
else
  commands
fi
```

Spaces around `[` and `]` and around operators are required.

### 2.2 File Test Operators

Use these inside `[ ]` or `[[ ]]` to check files and directories:

| Operator | Meaning |
|----------|---------|
| `-f <file>` | True if path exists and is a **regular file** |
| `-d <file>` | True if path exists and is a **directory** |
| `-e <file>` | True if path **exists** (any type) |
| `-s <file>` | True if file exists and is **not empty** |
| `-r <file>` | True if file is **readable** |
| `-w <file>` | True if file is **writable** |
| `-x <file>` | True if file is **executable** |

**Examples:**

```bash
if [ -f "config.txt" ]; then
  echo "Config file exists"
fi

if [ ! -d "backup" ]; then
  mkdir backup
fi
```

`!` negates the condition.

### 2.3 Loops

**`for` loop** — iterate over a list or range:

```bash
for i in 1 2 3; do
  echo "Number: $i"
done

for file in *.txt; do
  echo "Processing: $file"
done
```

**`while` loop** — run while a condition is true:

```bash
while [ condition ]; do
  commands
done
```

Loops are used for batch processing, repeating commands, and iterating over files or output.

---

## 3. File Permissions and Management

### 3.1 File and Directory Management

| Command | Purpose |
|---------|---------|
| `mkdir <dir>` | Create directory |
| `cd <dir>` | Change directory |
| `touch <file>` | Create empty file or update timestamp |
| `cat > filename` | Create/overwrite file with stdin (type content, then Ctrl+D) |
| `ls` | List directory contents |
| `pwd` | Print current working directory |
| `cp <src> <dest>` | Copy file or directory (`-r` for recursive) |
| `mv <src> <dest>` | Move or rename |
| `rm <file>` | Remove file; `rm -r` for directories; **`rm -rf`** removes recursively and forcefully (use with caution) |

### 3.2 File Permissions (chmod)

Permissions control **read (r)**, **write (w)**, and **execute (x)** for three roles: **user (owner)**, **group**, and **other**.

**Symbolic notation:** `chmod u+x script.sh` (add execute for owner), `chmod g-w file`, `chmod o=r file`.

**Octal notation:** Each digit is the sum of r=4, w=2, x=1.

- **755** — owner rwx, group and other r-x (common for scripts).
- **644** — owner rw-, group and other r-- (common for config files).

**`rwx`** in listings means read, write, and execute for that role.

### 3.3 Access Control Lists (ACLs)

**ACLs** extend the standard user/group/other model with **per-user and per-group** rules, so you can grant access to specific users or groups without changing the file’s main group.

| Command | Purpose |
|---------|---------|
| **getfacl** `<file>` | Display ACL entries for a file or directory |
| **setfacl -m** `"u:username:rwx"` `<file>` | Grant permissions to a user |
| **setfacl -m** `"g:groupname:rwx"` `<file>` | Grant permissions to a group |
| **setfacl -x** `"u:username"` `<file>` | Remove one ACL entry |
| **setfacl -b** `<file>` | Remove all extended ACL entries |
| **setfacl -dm** `"entry"` `<dir>` | Set **default ACL** on a directory (new files inherit it) |

ACLs require the filesystem to be mounted with the **acl** option. The **mask** limits the maximum permissions that named users and groups can have; **effective** permissions are what the mask allows.

---

## 4. Common Commands

These are essential for scripting and daily use:

| Command | Description |
|---------|-------------|
| **ls** | List files; `-l` long format, `-a` include hidden |
| **pwd** | Print working directory |
| **cd** | Change directory; `cd ~` home, `cd -` previous |
| **rm** | Remove; `-r` recursive, `-f` force (use carefully) |
| **cp** | Copy; `-r` for directories |
| **mv** | Move or rename |

**Caution:** `rm -rf` deletes recursively without prompting. Always double-check paths (e.g. avoid `rm -rf /` or variables that might be empty).

---

## 5. String Manipulation and Utilities

### 5.1 Text Transformation: tr, sed, awk

| Tool | Typical use |
|------|-------------|
| **tr** | Translate or delete characters (e.g. case change, strip characters). |
| **sed** | Stream editor: search/replace, line selection, in-place edit. |
| **awk** | Pattern-based processing: columns, fields, calculations. |

**Example — uppercasing with `tr`:**

```bash
echo "$STRING" | tr '[:lower:]' '[:upper:]'
# or (less portable): tr [a-z] [A-Z]
```

### 5.2 find and locate

**find** — search by path, name, type, size, time, etc.

```bash
find <path> -name "<pattern>"
find . -type f -name "*.log"
find /var -mtime +7   # modified more than 7 days ago
```

**locate** — fast search by filename using a pre-built index (e.g. `locate filename`). Results may be slightly outdated until the index is updated.

### 5.3 Regular Expressions (Regex)

**Regular expressions** are used for pattern matching in **grep**, **sed**, **awk**, and **bash** `[[ string =~ regex ]]`. They were only briefly mentioned in class but are important for text processing and validation in scripts.

---

## 6. Advanced Scripting Concepts

### 6.1 Functions

**Functions** group reusable logic and keep scripts organized.

**Definition (two common forms):**

```bash
my_function() {
  commands
}

# or
function my_function {
  commands
}
```

**Call:** `my_function` (no parentheses).

**Return value:** The `return` statement sets only the **exit status** (0–255), not arbitrary data. Exit status 0 means success; non-zero often means error. The caller can use `$?` after the call to read the status.

**Returning data:** Use one of:

- **Echo and capture:** `result=$(my_function)` if the function prints the value with `echo`.
- **Global variable:** Function assigns to a variable that the caller reads.

**Example:**

```bash
greet() {
  echo "Hello, $1"
}
greet "World"

check_file() {
  [ -f "$1" ] && return 0 || return 1
}
check_file "config.txt" && echo "Exists" || echo "Missing"
```

### 6.2 User and Group Management (Automation)

Shell scripts can automate **user** and **group** management:

| Command | Purpose |
|---------|---------|
| **groupadd** `<group>` | Create a group |
| **useradd** `<user>` | Create a user; options set home, shell, etc. |
| **usermod** | Modify user (e.g. add to group with `-aG`) |
| **userdel**, **groupdel** | Remove user or group |

Scripts might set default shell (`-s /bin/bash`), home directory (`-d`), welcome message (e.g. in `/etc/motd` or profile), and permissions on home directories. Often run with **sudo** or as root.

---

## 7. Summary

- **Shell scripts** automate tasks in Linux/UNIX; start with a **shebang** (e.g. `#!/usr/bin/env bash`), make executable with **chmod +x**, and run with `./script.sh`.
- **Variables:** `name=value`, use `$name`; **read -p** for input; quote when values may contain spaces or special characters.
- **Conditionals:** **if [ condition ]; then ... fi**; use **-f**, **-d**, **-e**, etc. for file tests.
- **Loops:** **for** and **while** for iteration over lists or conditions.
- **Files:** **mkdir**, **cd**, **touch**, **cp**, **mv**, **rm**; use **rm -rf** with care.
- **Permissions:** **chmod** (symbolic or octal); **ACLs** (**getfacl**, **setfacl**) for finer-grained control.
- **Text:** **tr**, **sed**, **awk** for transformation; **find** (and **locate**) for searching; **regex** for patterns.
- **Functions:** Define with `name() { ... }`, call by name; **return** sets exit status; use `echo` + command substitution or globals to pass data.
- **Automation:** Scripts can orchestrate **useradd**, **groupadd**, and related commands for user and group management.

These concepts form the basis for writing clear, maintainable automation and administration scripts in Linux environments.