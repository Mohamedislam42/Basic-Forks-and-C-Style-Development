# 🔧 Basic Forks and C Style Development

This project demonstrates **process creation**, **process management**, **compilation**, **linking**, and **loading** of C programs using **GCC**, **Make**, and various Linux tools.

---

## 📂 Project Structure

```
.
├── process_creation.c
├── file1.c
├── file2.c
├── simple_program.c
├── Makefile
├── LICENSE
└── README.md
```

---

## 🚀 How to Build & Run

### 🧱 Build all programs

```bash
make
```

### ▶️ Run programs

```bash
make run_process_creation
make run_output_program
make run_simple_program
```

### 🧹 Clean build files

```bash
make clean
```

---

## 💻 Code Examples & Explanations

### 1️⃣ Exercise 1: Process Creation with `fork()`

**File:** `process_creation.c`

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();
    
    if (pid == 0) {
        printf("This is the child process. PID: %d\n", getpid());
    } else if (pid > 0) {
        printf("This is the parent process. PID: %d\n", getpid());
    } else {
        printf("Fork failed!\n");
    }
    
    return 0;
}
```

**What it does:**
- The `fork()` system call creates a new process by duplicating the calling process
- The new process is called the **child process**, and the original is the **parent process**
- `fork()` returns:
  - `0` to the child process
  - The child's PID (positive number) to the parent process
  - `-1` if the fork failed
- Both processes continue execution from the point after `fork()`
- `getpid()` returns the process ID of the calling process

**Run:**
```bash
./process_creation
```

**Expected Output:**
```
This is the parent process. PID: 12345
This is the child process. PID: 12346
```

---

### 2️⃣ Exercise 2: Starting Background Processes

**Command:**
```bash
sleep 300 &
```

**What it does:**
- The `&` operator starts a process in the background
- This allows you to continue using the terminal while the process runs
- `sleep 300` pauses for 300 seconds (5 minutes)

**List background jobs:**
```bash
jobs
```

**Expected Output:**
```
[1]+  Running                 sleep 300 &
```

---

### 3️⃣ Exercise 3: Stopping Processes

**Find the process:**
```bash
ps aux | grep sleep
```

**What it does:**
- `ps aux` lists all running processes
- `grep sleep` filters to show only lines containing "sleep"
- The second column shows the PID (Process ID)

**Stop the process:**
```bash
kill <PID>
```

**What it does:**
- `kill` sends a signal to a process
- By default, it sends SIGTERM (terminate signal)
- Replace `<PID>` with the actual process ID

**Verify termination:**
```bash
ps aux | grep sleep
```

---

### 4️⃣ Exercise 4: Pausing and Resuming Processes

**Pause a process:**
```bash
kill -STOP <PID>
```

**What it does:**
- Sends SIGSTOP signal to pause the process
- The process stops executing but remains in memory
- Can be resumed later

**Resume the process:**
```bash
kill -CONT <PID>
```

**What it does:**
- Sends SIGCONT signal to resume the process
- The process continues from where it was paused

---

### 5️⃣ Exercise 5: Separate Compilation and Linking

**File 1:** `file1.c`

```c
#include <stdio.h>

void hello() {
    printf("Hello this is the modified file1!\n");
}
```

**File 2:** `file2.c`

```c
void hello();

int main() {
    hello();
    return 0;
}
```

**What it does:**
- `file1.c` contains the implementation of the `hello()` function
- `file2.c` contains the main program that calls `hello()`
- The declaration `void hello();` in `file2.c` tells the compiler that `hello()` exists somewhere
- The linker combines both files to create a working executable

**Compile and link:**
```bash
gcc file1.c file2.c -o output_program
```

**Run:**
```bash
./output_program
```

**Output:**
```
Hello this is the modified file1!
```

---

### 6️⃣ Exercise 6: Dynamic Linking with `ldd`

**File:** `simple_program.c`

```c
#include <stdio.h>

int main() {
    printf("This is a simple program.\n");
    return 0;
}
```

**What it does:**
- A simple program that prints a message
- Uses the standard C library for `printf()`
- Demonstrates dynamic linking to shared libraries

**Compile:**
```bash
gcc simple_program.c -o simple_program
```

**Run:**
```bash
./simple_program
```

**Output:**
```
This is a simple program.
```

**Inspect dynamic libraries:**
```bash
ldd simple_program
```

**Example Output:**
```
linux-vdso.so.1 (0x00007ffd8e9f0000)
libc.so.6 => /usr/lib/x86_64-linux-gnu/libc.so.6 (0x00007f8a9c200000)
/lib64/ld-linux-x86-64.so.2 (0x00007f8a9c400000)
```

**Libraries explained:**
- **linux-vdso.so.1**: Virtual dynamic shared object for system calls
- **libc.so.6**: Standard C library (contains printf, malloc, etc.)
- **/lib64/ld-linux-x86-64.so.2**: Dynamic linker/loader itself

---

## 🧠 Key Concepts Explained

### 🔗 What is the Linker?

The **linker** is a program that combines one or more object files (.o) into a single executable program.

**Jobs of the Linker:**
1. **Symbol Resolution**: Resolves references between object files (e.g., when `file2.c` calls `hello()` defined in `file1.c`)
2. **Address Binding**: Assigns final memory addresses to functions and variables
3. **Library Linking**: Links external libraries (static or dynamic) to the program
4. **Relocation**: Adjusts code and data to their final memory locations
5. **Generating Executable**: Creates the final executable file format (e.g., ELF on Linux)

**Types of Linking:**
- **Static Linking**: Libraries are copied into the executable at compile time
- **Dynamic Linking**: Libraries are loaded at runtime (saves disk space and memory)

---

### ⚙️ What is the Loader?

The **loader** is a part of the operating system that loads programs into memory and prepares them for execution.

**Jobs of the Loader:**
1. **Loading**: Reads the executable file from disk into memory
2. **Memory Allocation**: Allocates memory segments for code, data, stack, and heap
3. **Dynamic Linking**: Loads shared libraries (.so files) required by the program
4. **Relocation**: Adjusts addresses if the program is loaded at a different memory location
5. **Initialization**: Sets up the execution environment (command-line arguments, environment variables)
6. **Transfer Control**: Starts program execution by jumping to the entry point (usually `main()`)

**When you run a program:**
```bash
./simple_program
```
The loader:
1. Loads `simple_program` into memory
2. Loads shared libraries (`libc.so.6`, etc.)
3. Resolves dynamic symbols
4. Executes the program starting from `main()`

---

## 🛠️ Makefile Explanation

The Makefile automates compilation, linking, and cleanup tasks.

**Key targets:**
- `all`: Builds all programs
- `process_creation`: Compiles the fork example
- `output_program`: Compiles and links file1.c and file2.c
- `simple_program`: Compiles the simple program
- `run_*`: Executes each program
- `clean`: Removes all compiled files

**Benefits of using Make:**
- Automates repetitive compilation commands
- Only recompiles files that have changed
- Makes project building consistent and reproducible
- Simplifies complex build processes

---

## ✨ Expected Outputs

| Program              | Output                                      |
|----------------------|---------------------------------------------|
| `process_creation`   | Shows parent and child process PIDs         |
| `output_program`     | `Hello this is the modified file1!`         |
| `simple_program`     | `This is a simple program.`                 |

---

## 📦 Dynamic Libraries Used

The `simple_program` typically links against:
- **libc.so.6**: Standard C library
- **linux-vdso.so.1**: Virtual system call interface
- **ld-linux-x86-64.so.2**: Dynamic linker/loader

---

## 📋 Requirements

- **GCC** (GNU Compiler Collection)
  ```bash
  sudo apt install gcc
  ```
- **Make** (build automation tool)
  ```bash
  sudo apt install make
  ```
- **Linux** operating system

---

## 📜 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Author

**Mohamed Islam**

---

## 🧰 Tools Used

- **GCC**: GNU Compiler Collection
- **Make**: Build automation tool
- **LDD**: List dynamic dependencies
- **ps**: Process status utility
- **kill**: Send signals to processes
- **fork()**: System call for process creation

---
