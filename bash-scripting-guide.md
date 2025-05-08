# Comprehensive Bash Scripting Guide for Beginners

## Table of Contents
- [Introduction to Bash](#introduction-to-bash)
- [Getting Started](#getting-started)
- [Bash Syntax Fundamentals](#bash-syntax-fundamentals)
- [Variables and Data Types](#variables-and-data-types)
- [Common Commands Reference](#common-commands-reference)
- [Control Structures](#control-structures)
- [Functions](#functions)
- [Input and Output](#input-and-output)
- [Command Substitution](#command-substitution)
- [Error Handling](#error-handling)
- [Regular Expressions](#regular-expressions)
- [Bash vs. Python](#bash-vs-python)
- [Using Bash and Python Together](#using-bash-and-python-together)
- [Best Practices](#best-practices)
- [Practical Examples](#practical-examples)
- [Advanced Topics](#advanced-topics)
- [Resources for Further Learning](#resources-for-further-learning)

## Introduction to Bash

### What is Bash?

Bash (Bourne Again Shell) is a command processor and scripting language that provides a command-line interface for interacting with Unix-like operating systems. It was created by Brian Fox in 1989 as a free software replacement for the Bourne Shell.

### Why Learn Bash?

1. **System Administration**: Essential for managing Unix/Linux systems
2. **Automation**: Automate repetitive tasks and system processes
3. **Development Environment**: Configure and manage development environments
4. **Career Opportunities**: Valuable skill for DevOps, system administration, and software development
5. **Ubiquity**: Pre-installed on macOS, Linux, and available on Windows via WSL

### Bash vs. Other Shells

| Shell | Description | Distinguishing Features |
|-------|-------------|-------------------------|
| Bash (Bourne Again Shell) | Default for most Linux distributions and macOS (until Catalina) | Balanced features, good compatibility |
| Zsh | Default shell in macOS (Catalina and later) | Advanced auto-completion, theming, plugins |
| Fish | User-friendly alternative | Syntax highlighting, auto-suggestions |
| PowerShell | Microsoft's task automation framework | Object-oriented, integrated with .NET |

## Getting Started

### First Script

Create a file named `hello.sh`:

```bash
#!/bin/bash

# This is a comment
echo "Hello, World!"  # This prints the text to the console
```

The `#!/bin/bash` is called a "shebang" or "hashbang" line. It tells the system which interpreter to use for executing the script.

### Making Scripts Executable

```bash
chmod +x hello.sh  # Makes the script executable
./hello.sh         # Runs the script
```

### Running Bash Scripts

There are multiple ways to run a Bash script:

1. **Execute directly** (requires executable permission):
   ```bash
   ./script.sh
   ```

2. **Use bash command**:
   ```bash
   bash script.sh
   ```

3. **Source the script** (runs in current shell context):
   ```bash
   source script.sh
   # or
   . script.sh
   ```

## Bash Syntax Fundamentals

### Comments

```bash
# This is a single-line comment

: '
This is a
multi-line comment
in Bash
'
```

### Command Structure

Commands in Bash follow this general structure:

```
command [options] [arguments]
```

For example:
```bash
ls -la /home/user  # List all files in long format in /home/user directory
```

### Command Separators

```bash
command1; command2    # Run command1 then command2
command1 && command2  # Run command2 only if command1 succeeds
command1 || command2  # Run command2 only if command1 fails
command1 | command2   # Pipe the output of command1 to command2
```

### Quoting

Bash has three types of quoting:

1. **No quotes**: Variable names are expanded, wildcards are processed
   ```bash
   echo $HOME/*.txt
   ```

2. **Double quotes** (`"`): Variable names are expanded, wildcards are not processed
   ```bash
   echo "$HOME/*.txt"
   ```

3. **Single quotes** (`'`): Everything is treated literally
   ```bash
   echo '$HOME/*.txt'
   ```

## Variables and Data Types

### Variable Assignment

```bash
# No spaces around the equals sign
name="John"
age=30
```

### Variable Usage

```bash
echo $name        # Prints: John
echo ${name}      # Same as above, but with explicit boundaries
echo "${name}'s"  # Prints: John's (braces help delimit the variable name)
```

### Variable Types

Bash variables are untyped by default, but they are generally used as:

1. **Strings**:
   ```bash
   message="Hello there"
   ```

2. **Integers**:
   ```bash
   count=42
   ```

3. **Arrays**:
   ```bash
   fruits=("apple" "banana" "cherry")
   echo ${fruits[0]}   # Prints: apple
   echo ${fruits[@]}   # Prints: apple banana cherry
   echo ${#fruits[@]}  # Prints: 3 (array length)
   ```

4. **Associative Arrays** (Bash 4+):
   ```bash
   declare -A user
   user[name]="John"
   user[age]=30
   echo ${user[name]}  # Prints: John
   ```

### Special Variables

| Variable | Description |
|----------|-------------|
| `$0` | Script name |
| `$1` to `$9` | First 9 arguments passed to script |
| `${10}` and beyond | Additional arguments |
| `$#` | Number of arguments |
| `$@` | All arguments as separate strings |
| `$*` | All arguments as a single string |
| `$?` | Exit status of the last command |
| `$$` | Process ID of the current shell |
| `$!` | Process ID of the last background command |
| `$USER` | Current username |
| `$HOME` | Home directory path |
| `$PWD` | Current working directory |
| `$RANDOM` | Random number between 0 and 32767 |
| `$LINENO` | Current line number in the script |

### Environment Variables

```bash
# Display all environment variables
env

# Access specific environment variables
echo $PATH
echo $HOME
echo $USER
```

## Common Commands Reference

### File Operations

| Command | Description | Example |
|---------|-------------|---------|
| `ls` | List directory contents | `ls -la` |
| `cd` | Change directory | `cd /home/user` |
| `pwd` | Print working directory | `pwd` |
| `mkdir` | Create directory | `mkdir -p path/to/dir` |
| `rm` | Remove files or directories | `rm -rf dir` |
| `cp` | Copy files or directories | `cp -r source dest` |
| `mv` | Move/rename files or directories | `mv old new` |
| `touch` | Create empty file or update timestamp | `touch file.txt` |
| `cat` | Display file contents | `cat file.txt` |
| `less` | View file contents page by page | `less file.txt` |
| `head` | Display first lines of file | `head -n 10 file.txt` |
| `tail` | Display last lines of file | `tail -n 10 file.txt` |
| `grep` | Search for patterns in files | `grep "pattern" file.txt` |
| `find` | Find files and directories | `find . -name "*.txt"` |
| `chmod` | Change file permissions | `chmod +x script.sh` |
| `chown` | Change file owner and group | `chown user:group file` |

### Text Processing

| Command | Description | Example |
|---------|-------------|---------|
| `grep` | Search for patterns | `grep -i "text" file.txt` |
| `sed` | Stream editor for text filtering | `sed 's/old/new/g' file.txt` |
| `awk` | Pattern scanning/processing language | `awk '{print $1}' file.txt` |
| `sort` | Sort lines of text files | `sort -n file.txt` |
| `uniq` | Report or filter repeated lines | `uniq -c file.txt` |
| `cut` | Remove sections from lines | `cut -d, -f1 file.csv` |
| `tr` | Translate or delete characters | `tr 'a-z' 'A-Z' < file.txt` |
| `wc` | Count lines, words, and characters | `wc -l file.txt` |

### System Information

| Command | Description | Example |
|---------|-------------|---------|
| `uname` | Print system information | `uname -a` |
| `hostname` | Show system hostname | `hostname` |
| `top` | Display processes | `top` |
| `ps` | Report process status | `ps aux` |
| `df` | Report disk space usage | `df -h` |
| `du` | Estimate file space usage | `du -sh dir` |
| `free` | Display memory usage | `free -h` |
| `who` | Show who is logged in | `who` |
| `whoami` | Print current user | `whoami` |
| `date` | Display date and time | `date` |

### Networking

| Command | Description | Example |
|---------|-------------|---------|
| `ping` | Test network connectivity | `ping google.com` |
| `curl` | Transfer data from/to servers | `curl -O https://example.com/file` |
| `wget` | Non-interactive download | `wget https://example.com/file` |
| `ssh` | Secure shell client | `ssh user@host` |
| `scp` | Secure copy | `scp file.txt user@host:/path` |
| `netstat` | Network statistics | `netstat -tuln` |
| `ss` | Socket statistics (modern netstat) | `ss -tuln` |
| `ifconfig`/`ip` | Network interface configuration | `ifconfig` or `ip addr` |
| `host` | DNS lookup | `host example.com` |
| `dig` | DNS lookup utility | `dig example.com` |
| `telnet` | Connect to remote host on specific port | `telnet host.com 80` |
| `nc` | Netcat - versatile networking utility | `nc -zv host.com 80` |
| `traceroute` | Trace packet route to host | `traceroute google.com` |
| `nmap` | Network exploration and security scanning | `nmap -p 1-100 host.com` |
| `tcpdump` | Capture and analyze network traffic | `tcpdump -i eth0 port 80` |
| `mtr` | Combines ping and traceroute | `mtr google.com` |
| `whois` | Query domain registration info | `whois example.com` |
| `rsync` | Fast, versatile file copying | `rsync -avz dir/ user@host:/path` |
| `iptables` | Configure firewall rules | `iptables -L` |
| `arp` | View/modify ARP cache | `arp -a` |

## Control Structures

### Conditional Statements

#### if-else Statement

```bash
#!/bin/bash

# Basic if-else statement
if [ "$1" -gt 10 ]; then
    echo "Number is greater than 10"  # This runs when the condition is true
elif [ "$1" -eq 10 ]; then
    echo "Number is equal to 10"      # This runs when the first condition is false but this one is true
else
    echo "Number is less than 10"     # This runs when all conditions are false
fi
```

#### Test Operators

##### File Test Operators

| Operator | Description |
|----------|-------------|
| `-e file` | True if file exists |
| `-f file` | True if file exists and is a regular file |
| `-d file` | True if file exists and is a directory |
| `-r file` | True if file exists and is readable |
| `-w file` | True if file exists and is writable |
| `-x file` | True if file exists and is executable |
| `-s file` | True if file exists and has size greater than zero |

##### String Test Operators

| Operator | Description | Example |
|----------|-------------|---------|
| `-z string` | True if string is empty | `[ -z "$var" ]` |
| `-n string` | True if string is not empty | `[ -n "$var" ]` |
| `string` | True if string is not empty (implicit test) | `[ "$var" ]` |
| `string1 = string2` | True if strings are equal | `[ "$a" = "$b" ]` |
| `string1 != string2` | True if strings are not equal | `[ "$a" != "$b" ]` |
| `string1 < string2` | True if string1 sorts before string2 lexicographically | `[ "$a" \< "$b" ]` |
| `string1 > string2` | True if string1 sorts after string2 lexicographically | `[ "$a" \> "$b" ]` |

**Important Notes:**
- The operator must come first inside the brackets: `[ -n "$1" ]` (correct) vs `[ "$1" -n ]` (incorrect)
- Always quote variables to handle empty strings and spaces: `[ -n "$var" ]` not `[ -n $var ]`
- The test `[ "$var" ]` is equivalent to `[ -n "$var" ]` (both test if string is non-empty)

##### Numeric Comparison Operators

| Operator | Description |
|----------|-------------|
| `num1 -eq num2` | True if numbers are equal |
| `num1 -ne num2` | True if numbers are not equal |
| `num1 -lt num2` | True if num1 is less than num2 |
| `num1 -le num2` | True if num1 is less than or equal to num2 |
| `num1 -gt num2` | True if num1 is greater than num2 |
| `num1 -ge num2` | True if num1 is greater than or equal to num2 |

##### Logical Operators

| Operator | Description |
|----------|-------------|
| `! expression` | True if expression is false |
| `expr1 -a expr2` | True if both expressions are true (AND) |
| `expr1 -o expr2` | True if either expression is true (OR) |
| `expr1 && expr2` | True if both expressions are true (AND) |
| `expr1 || expr2` | True if either expression is true (OR) |

#### Case Statement

```bash
#!/bin/bash

# Case statement for handling multiple conditions cleanly
case "$1" in
    start)
        echo "Starting the service"  # This runs when $1 equals "start"
        # start commands here
        ;;
    stop)
        echo "Stopping the service"  # This runs when $1 equals "stop"
        # stop commands here
        ;;
    restart)
        echo "Restarting the service"  # This runs when $1 equals "restart"
        # restart commands here
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"  # This runs when $1 doesn't match any pattern
        exit 1
        ;;
esac
```

### Loops

#### For Loop

```bash
#!/bin/bash

# Basic for loop (list form)
for name in John Sara Mark Lisa; do
    echo "Hello, $name!"  # This line executes once for each item in the list
done

# For loop with range
for i in {1..5}; do  # Loops through numbers 1, 2, 3, 4, 5
    echo "Number: $i"
done

# C-style for loop
for ((i=0; i<5; i++)); do  # Initialize i=0, continue while i<5, increment i each loop
    echo "Count: $i"
done

# Iterating over files
for file in *.txt; do  # Loops through all .txt files in the current directory
    echo "Processing $file..."
    # file processing commands here
done

# Iterating over array elements
fruits=("apple" "banana" "cherry")
for fruit in "${fruits[@]}"; do  # Loop through each element in the array
    echo "I like $fruit"
done
```

#### While Loop

```bash
#!/bin/bash

# Basic while loop
count=1
while [ $count -le 5 ]; do  # Continue looping as long as count is less than or equal to 5
    echo "Count: $count"
    count=$((count + 1))    # Increment count by 1
done

# Reading file line by line
while read -r line; do  # Read each line from the file
    echo "Line: $line"
done < input.txt

# Infinite loop (with break)
while true; do          # Loop forever until break is encountered
    echo "Enter a number (0 to exit):"
    read num
    if [ "$num" -eq 0 ]; then
        break           # Exit the loop when user enters 0
    fi
    echo "You entered: $num"
done
```

#### Until Loop

```bash
#!/bin/bash

# Basic until loop
count=1
until [ $count -gt 5 ]; do  # Continue looping until count is greater than 5
    echo "Count: $count"
    count=$((count + 1))    # Increment count by 1
done
```

#### Loop Control

```bash
#!/bin/bash

# Break statement to exit a loop
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        echo "Breaking at $i"
        break            # Exit the loop immediately
    fi
    echo "Number: $i"
done

# Continue statement to skip current iteration
for i in {1..5}; do
    if [ $i -eq 3 ]; then
        echo "Skipping $i"
        continue         # Skip to next iteration
    fi
    echo "Number: $i"
done

# Select loop for menus
echo "Select a fruit:"
select fruit in "Apple" "Banana" "Cherry" "Quit"; do
    case $fruit in
        "Quit")
            echo "Goodbye!"
            break        # Exit the select loop
            ;;
        *)
            echo "You selected $fruit"
            ;;
    esac
done
```

## Functions

### Defining Functions

```bash
#!/bin/bash

# Function definition
greet() {
    echo "Hello, $1!"  # $1 refers to the first argument passed to the function
}

# Function with return value
add() {
    local result=$(($1 + $2))  # 'local' makes the variable scope limited to the function
    echo $result               # Use 'echo' to return a value
}

# Function with local variables and return status
validate_age() {
    local age=$1
    if [[ $age =~ ^[0-9]+$ ]]; then
        if [ $age -ge 18 ]; then
            return 0  # Return success (0 means success in Bash)
        else
            return 1  # Return failure code 1
        fi
    else
        return 2  # Return failure code 2
    fi
}
```

### Calling Functions

```bash
#!/bin/bash

# Call function
greet "John"

# Capture function output
sum=$(add 5 3)
echo "5 + 3 = $sum"

# Use function return status
validate_age 25
case $? in
    0)
        echo "Age is valid and person is an adult"
        ;;
    1)
        echo "Age is valid but person is not an adult"
        ;;
    2)
        echo "Invalid age format"
        ;;
esac
```

### Function Arguments

```bash
#!/bin/bash

# Function with multiple arguments
introduce() {
    echo "Name: $1"    # First argument
    echo "Age: $2"     # Second argument
    echo "City: $3"    # Third argument
    echo "All args: $@"  # All arguments as list
    echo "Number of args: $#"  # Number of arguments
}

# Call function with arguments
introduce "John" 30 "New York"
```

## Input and Output

### Standard Input

```bash
#!/bin/bash

# Read user input
echo "What is your name?"
read name              # Read a single value into the variable 'name'

# Read with prompt
read -p "Enter your age: " age  # -p displays a prompt

# Read with timeout
read -t 5 -p "Quick! Enter something (5 sec timeout): " input  # -t sets timeout in seconds

# Read password (no echo)
read -s -p "Enter password: " password  # -s suppresses input display
echo  # Add newline after password input

# Read multiple values
echo "Enter your first and last name:"
read first last        # First word goes to 'first', second to 'last'

# Read into array
echo "Enter some fruits:"
read -a fruits         # Read words into array 'fruits'
echo "You entered ${#fruits[@]} fruits."
```

### Standard Output and Error

```bash
#!/bin/bash

# Basic output
echo "Standard output"  # Prints to stdout (file descriptor 1)

# Error output
echo "Error message" >&2  # Redirects to stderr (file descriptor 2)

# Formatted output
printf "Name: %s, Age: %d\n" "John" 30  # %s for string, %d for decimal, \n for newline

# Output to file
echo "Writing to file" > output.txt     # Overwrites file
echo "Appending to file" >> output.txt  # Appends to file

# Suppress output
command > /dev/null     # Redirect stdout to null device
command 2> /dev/null    # Redirect stderr to null device
command &> /dev/null    # Redirect both stdout and stderr to null device
```

### Redirections

```bash
#!/bin/bash

# Input redirections
wc -l < file.txt          # Redirect file.txt as input to wc

# Output redirections
ls -la > listing.txt      # Redirect stdout to file (overwrite)
ls -la >> listing.txt     # Redirect stdout to file (append)
ls -la 2> errors.txt      # Redirect stderr to file
ls -la &> all_output.txt  # Redirect both stdout and stderr to file

# Pipe command output to another command
ls -la | grep "\.txt"     # Pipe ls output to grep

# Here document (multi-line input)
cat << EOF > config.ini
[Settings]
User = John
Path = /home/john
Debug = true
EOF

# Here string (single-line input)
grep "apple" <<< "I like apple pie"
```

## Command Substitution

### Basic Command Substitution

```bash
#!/bin/bash

# Old syntax (backticks)
current_dir=`pwd`
echo "Current directory: $current_dir"

# New syntax (preferred)
current_date=$(date)
echo "Current date: $current_date"

# Nested command substitution
file_count=$(ls -1 $(pwd) | wc -l)  # Count files in current directory
echo "Number of files: $file_count"

# Using with command options
files=$(find . -name "*.txt" -mtime -7)  # Find .txt files modified in the last 7 days
echo "Recent text files: $files"
```

### Arithmetic Operations

```bash
#!/bin/bash

# Basic arithmetic
a=5
b=3

# Addition
sum=$((a + b))
echo "Sum: $sum"

# Subtraction
diff=$((a - b))
echo "Difference: $diff"

# Multiplication
product=$((a * b))
echo "Product: $product"

# Division (integer division)
div=$((a / b))
echo "Division: $div"

# Modulus (remainder)
mod=$((a % b))
echo "Remainder: $mod"

# Exponentiation
power=$((a ** b))
echo "Power: $power"

# Increment
a=$((a + 1))  # or: ((a++)) or: ((++a))
echo "Incremented: $a"

# Decrement
b=$((b - 1))  # or: ((b--)) or: ((--b))
echo "Decremented: $b"

# Compound assignment
a=$((a + 5))  # or: ((a += 5))
echo "Added 5: $a"

# Using expr (older method)
result=$(expr $a + $b)
echo "Result (using expr): $result"

# Using bc for floating-point arithmetic
floating_result=$(echo "scale=2; 5/3" | bc)
echo "Floating-point result: $floating_result"
```

## Error Handling

### Exit Status

```bash
#!/bin/bash

# Check exit status of previous command
ls /nonexistent-directory
if [ $? -ne 0 ]; then
    echo "Error: Directory not found."  # This runs when the exit status is not 0
fi

# Using exit status in condition directly
if grep -q "pattern" file.txt; then
    echo "Pattern found"
else
    echo "Pattern not found"
fi

# Setting exit status
exit 0  # Success
exit 1  # General error
```

### Error Trapping

```bash
#!/bin/bash

# Set a trap for script termination
trap "echo 'Script is terminating'; exit" SIGINT SIGTERM

# Set a trap for specific errors
trap "echo 'Error occurred at line $LINENO'; exit 1" ERR

# Clean up function
cleanup() {
    echo "Performing cleanup..."
    # Delete temporary files, etc.
}

# Set trap to call cleanup function on exit
trap cleanup EXIT

# Rest of the script...
echo "Script running..."
sleep 5  # Do something
echo "Script completed."  # This will execute before the cleanup function
```

### Debugging

```bash
#!/bin/bash

# Enable debugging
set -x  # Print commands and their arguments as they are executed

# Some commands for demonstration
echo "Debug mode is on"
ls -la
date

# Disable debugging
set +x

# Other debugging options
set -e  # Exit immediately if a command exits with non-zero status
set -u  # Treat unset variables as an error
set -o pipefail  # Return value of a pipeline is the value of the last command to exit with non-zero status

# Combine options
set -euo pipefail  # Strict mode

# Debug specific section
{
    set -x
    # Only debug these commands
    echo "Debugging this section"
    grep "pattern" file.txt
    set +x
} 2>/tmp/debug.log  # Redirect debug output to a file
```

## Regular Expressions

### Basic Pattern Matching

```bash
#!/bin/bash

# Simple pattern matching with grep
grep "apple" fruits.txt  # Find lines containing "apple"

# Case-insensitive matching
grep -i "apple" fruits.txt  # Match "apple", "Apple", "APPLE", etc.

# Match whole words only
grep -w "apple" fruits.txt  # Won't match "pineapple"

# Count matches
grep -c "apple" fruits.txt  # Output only the count of matching lines

# Invert match
grep -v "apple" fruits.txt  # Find lines NOT containing "apple"
```

### Regular Expression Syntax

```bash
#!/bin/bash

# Basic character classes
grep "[aeiou]" text.txt  # Match any vowel
grep "[0-9]" text.txt    # Match any digit
grep "[a-zA-Z]" text.txt # Match any letter

# Special character escaping
grep "\." text.txt       # Match literal period

# Anchors
grep "^Start" text.txt   # Match lines beginning with "Start"
grep "End$" text.txt     # Match lines ending with "End"

# Repetition operators
grep "a\{3\}" text.txt   # Match "aaa"
grep "a\+" text.txt      # Match one or more "a"s
grep "a\*" text.txt      # Match zero or more "a"s
grep "a\?" text.txt      # Match zero or one "a"

# Extended regular expressions (with -E flag)
grep -E "apple|orange" fruits.txt  # Match "apple" OR "orange"
grep -E "(red|green) apple" fruits.txt  # Match "red apple" or "green apple"
grep -E "[0-9]{3}-[0-9]{4}" contacts.txt  # Match phone numbers like "123-4567"
```

### Bash Regex Operator

```bash
#!/bin/bash

# Using Bash's =~ operator for regex matching
if [[ "apple123" =~ ^[a-z]+[0-9]+$ ]]; then
    echo "String matches pattern (letters followed by numbers)"
else
    echo "String doesn't match pattern"
fi

# Capturing groups
string="John Smith"
if [[ $string =~ ([A-Za-z]+)\ ([A-Za-z]+) ]]; then
    first_name="${BASH_REMATCH[1]}"  # First capture group
    last_name="${BASH_REMATCH[2]}"   # Second capture group
    echo "First name: $first_name"
    echo "Last name: $last_name"
fi

# Email validation example
validate_email() {
    local email=$1
    if [[ $email =~ ^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$ ]]; then
        return 0  # Valid
    else
        return 1  # Invalid
    fi
}

# Test email validation
emails=("user@example.com" "invalid-email" "another.user@domain.co.uk")
for email in "${emails[@]}"; do
    if validate_email "$email"; then
        echo "$email is valid"
    else
        echo "$email is invalid"
    fi
done
```

## Bash vs. Python

### Comparison Table

| Feature | Bash | Python |
|---------|------|--------|
| **Primary Purpose** | Shell scripting, system administration | General-purpose programming |
| **Syntax** | Terse, specialized for command execution | Clean, readable, consistent |
| **Typing** | Weakly typed (everything is a string) | Strongly typed with dynamic typing |
| **Data Structures** | Basic arrays, limited associative arrays | Rich (lists, dictionaries, sets, etc.) |
| **Error Handling** | Basic (exit codes and traps) | Comprehensive (try/except) |
| **Portability** | Unix/Linux focused (Windows via WSL/Cygwin) | Cross-platform |
| **Performance** | Good for I/O, poor for computation | Better for computation, good libraries |
| **Libraries** | Limited, relies on external commands | Extensive standard library and ecosystem |
| **Object-Oriented** | No | Yes |
| **Use Cases** | System tasks, automation, glue scripts | Web, data analysis, AI, applications |

### When to Use Bash vs. Python

#### Use Bash When:

1. **System Administration**: Managing files, processes, users
2. **Simple Automation**: Quick scripts for repetitive tasks
3. **Command Chaining**: When you need to pipe multiple commands together
4. **Environment Setup**: Configuring development environments
5. **Simple Text Processing**: Basic log analysis, file manipulations
6. **No Dependencies**: When you need to run on minimal systems without installing packages

#### Use Python When:

1. **Complex Logic**: When scripts include complex algorithms or data structures
2. **Data Processing**: Analyzing or manipulating data
3. **Web Operations**: Scraping, API interactions
4. **GUI Applications**: Creating graphical interfaces
5. **Cross-Platform**: When scripts need to run on multiple operating systems
6. **Large-Scale Scripts**: When scripts grow beyond a few hundred lines
7. **Testing**: When comprehensive testing is required

## Using Bash and Python Together

### Calling Python from Bash

```bash
#!/bin/bash

# Run a Python one-liner
python3 -c "print('Hello from Python!')"

# Run a Python script
python3 script.py

# Pass arguments to Python
python3 script.py arg1 arg2

# Capture Python output
result=$(python3 -c "import math; print(math.sqrt(16))")
echo "Square root of 16 is $result"

# Use Python for complex calculations
calculate_area() {
    local radius=$1
    # Use Python to calculate the area of a circle
    python3 -c "import math; print(math.pi * $radius**2)"
}

area=$(calculate_area 5)
echo "Area of circle with radius 5: $area"
```

### Calling Bash from Python

```python
#!/usr/bin/env python3

import subprocess
import os

# Run a simple command
subprocess.run(["ls", "-la"])

# Capture command output
result = subprocess.check_output(["date"]).decode().strip()
print(f"Current date: {result}")

# Run a shell command with shell expansion
subprocess.run("echo $HOME", shell=True)

# Run a command and handle errors
try:
    subprocess.run(["ls", "/nonexistent"], check=True)
except subprocess.CalledProcessError as e:
    print(f"Command failed with exit code {e.returncode}")

# Stream command output in real-time
process = subprocess.Popen(
    ["ping", "-c", "4", "google.com"],
    stdout=subprocess.PIPE,
    text=True
)

for line in process.stdout:
    print(line.strip())

# Run a Bash script from Python
subprocess.run(["bash", "script.sh", "arg1", "arg2"])

# Use os.system (older method)
os.system("echo Simple command")
```

### Python Script Template for System Tasks

```python
#!/usr/bin/env python3

"""
A Python script that performs system tasks traditionally done in Bash.
This shows how Python can be used as a system scripting language.
"""

import os
import sys
import subprocess
import argparse
import logging
import shutil
from pathlib import Path

def setup_logging():
    """Configure logging for the script."""
    logging.basicConfig(
        level=logging.INFO,
        format="%(asctime)s - %(levelname)s - %(message)s",
        datefmt="%Y-%m-%d %H:%M:%S"
    )

def parse_arguments():
    """Parse command line arguments."""
    parser = argparse.ArgumentParser(description="System administration tasks with Python")
    parser.add_argument("-d", "--directory", help="Target directory", default=".")
    parser.add_argument("-v", "--verbose", action="store_true", help="Enable verbose output")
    return parser.parse_args()

def list_files(directory):
    """List files in the specified directory (like 'ls')."""
    path = Path(directory)
    for item in path.iterdir():
        size = item.stat().st_size
        if item.is_file():
            logging.info(f"File: {item.name} - {size} bytes")
        else:
            logging.info(f"Dir:  {item.name}")

def disk_usage(directory):
    """Check disk usage of directory (like 'du')."""
    total_size = 0
    path = Path(directory)
    
    for item in path.glob("**/*"):
        if item.is_file():
            total_size += item.stat().st_size
    
    logging.info(f"Total size of {directory}: {total_size/1024/1024:.2f} MB")
    return total_size

def backup_directory(source, dest=None):
    """Backup a directory (like 'cp -r' or 'rsync')."""
    if dest is None:
        dest = f"{source}_backup"
    
    try:
        shutil.copytree(source, dest)
        logging.info(f"Successfully backed up {source} to {dest}")
        return True
    except Exception as e:
        logging.error(f"Backup failed: {e}")
        return False

def run_command(command):
    """Run a shell command and return the output."""
    try:
        result = subprocess.run(
            command,
            check=True,
            shell=True,
            text=True,
            capture_output=True
        )
        return result.stdout
    except subprocess.CalledProcessError as e:
        logging.error(f"Command failed: {e}")
        logging.error(f"Error output: {e.stderr}")
        return None

def main():
    """Main function that ties everything together."""
    setup_logging()
    args = parse_arguments()
    
    if args.verbose:
        logging.getLogger().setLevel(logging.DEBUG)
    
    logging.info(f"Starting system tasks on {args.directory}")
    
    # List files in directory
    list_files(args.directory)
    
    # Check disk usage
    disk_usage(args.directory)
    
    # Example of running a shell command from Python
    if os.name == "posix":  # Unix/Linux/MacOS
        uptime = run_command("uptime")
        logging.info(f"System uptime: {uptime}")
    
    # Backup the directory if it's not the current directory
    if args.directory != ".":
        backup_directory(args.directory)
    
    logging.info("All tasks completed")

if __name__ == "__main__":
    main()
```

## Best Practices

### Code Organization

1. **Use a Shebang**: Always include `#!/bin/bash` at the top of scripts
2. **Include Documentation**: Add comments explaining the script's purpose
3. **Use Functions**: Break down complex scripts into functions
4. **Consistent Naming**: Use lowercase for variables, uppercase for constants
5. **Indentation**: Use 2 or 4 spaces for indentation (be consistent)
6. **Sections**: Group related code into sections with comments

### Robustness

1. **Set Safe Options**:
   ```bash
   set -euo pipefail  # Exit on error, undefined vars, and pipe failures
   ```

2. **Check Command Existence**:
   ```bash
   command -v jq >/dev/null 2>&1 || { echo "jq is required but not installed"; exit 1; }
   ```

3. **Validate Input**:
   ```bash
   if [[ ! $age =~ ^[0-9]+$ ]]; then
       echo "Error: Age must be a number"
       exit 1
   fi
   ```

4. **Default Values**:
   ```bash
   # Set default value if variable is unset or empty
   param=${1:-default_value}
   ```

5. **Error Messages to stderr**:
   ```bash
   echo "Error: Something went wrong" >&2
   ```

### Performance

1. **Avoid Unnecessary Commands**: Minimize external program calls
2. **Use Built-ins**: Prefer Bash built-ins over external commands
3. **Minimize Subshells**: Limit use of `$()` when possible
4. **Read Files Efficiently**:
   ```bash
   # Good: Read file once
   while read -r line; do
       process "$line"
   done < file.txt
   
   # Bad: Read file in each iteration
   for line in $(cat file.txt); do
       process "$line"
   done
   ```

5. **Cache Results**: Store repeated computations

### Security

1. **Quote Variables**: Always quote variables to prevent word splitting
   ```bash
   file="my file.txt"
   cat "$file"  # Correct - treats as one argument
   cat $file    # Incorrect - splits on spaces
   ```

2. **Use Restricted Paths**: Specify full paths for security-sensitive commands
   ```bash
   /bin/rm -f "$file"  # Use full path to ensure you're using the correct command
   ```

3. **Avoid Injections**: Be careful with user input in commands
   ```bash
   # Dangerous:
   eval "ls $user_input"
   
   # Safer:
   ls -- "$user_input"
   ```

4. **Set Restrictive Permissions**:
   ```bash
   chmod 700 script.sh  # Only owner can read, write, execute
   ```

5. **Handle Sensitive Data Carefully**:
   ```bash
   # Don't echo passwords
   read -s -p "Password: " password
   ```

## Practical Examples

### Using String Operators

Let's look at a practical example of using string operators in a script. This example shows how to properly use the `-n` string operator:

```bash
#!/bin/bash
#
# Script to check if a host is reachable
# Usage: ./is_host_on_network.sh {IP_ADDRESS}

# Check if an argument was provided
if [ -n "$1" ]; then
    # The -n operator tests if a string is non-empty
    # This line means: "If the first argument ($1) is not empty, then..."
    ping -c 5 "$1"  # Ping the provided IP address 5 times
else
    # This runs when no argument is provided (empty string)
    echo "Usage: ./is_host_on_network.sh {IP_ADDRESS}"
fi
```

The same check could be written using implicit testing:

```bash
#!/bin/bash

# Alternative syntax: implicit non-empty string test
if [ "$1" ]; then
    # This is equivalent to [ -n "$1" ]
    # It means "if $1 is not empty"
    ping -c 5 "$1"
else
    echo "Usage: ./is_host_on_network.sh {IP_ADDRESS}"
fi
```

Important things to note:
1. The operator (`-n`) comes before the string being tested
2. We always quote the variable (`"$1"`) to handle spaces properly
3. The test must be inside square brackets with spaces: `[ -n "$1" ]`

### System Monitoring Script

```bash
#!/bin/bash
#
# system_monitor.sh - A script to monitor system resources
#
# Usage: ./system_monitor.sh [interval] [count]
#   interval: Time between checks in seconds (default: 5)
#   count: Number of checks to perform (default: 10)
#
# Example: ./system_monitor.sh 10 6  # Check every 10 seconds, 6 times

# Default values
interval=${1:-5}   # Default to 5 seconds if no argument provided
count=${2:-10}     # Default to 10 checks if no argument provided

# Validate input
if ! [[ "$interval" =~ ^[0-9]+$ ]]; then
    echo "Error: Interval must be a number" >&2
    exit 1
fi

if ! [[ "$count" =~ ^[0-9]+$ ]]; then
    echo "Error: Count must be a number" >&2
    exit 1
fi

# Function to format numbers with commas
format_number() {
    printf "%'d" "$1"
}

# Function to get CPU usage
get_cpu_usage() {
    # Get CPU usage as a percentage
    top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}' | awk '{printf "%.1f%%", $1}'
}

# Function to get memory usage
get_memory_usage() {
    # Get memory usage statistics
    local mem_total=$(free | grep Mem | awk '{print $2}')
    local mem_used=$(free | grep Mem | awk '{print $3}')
    local mem_percent=$(( 100 * mem_used / mem_total ))
    
    echo "$(format_number $mem_used)K / $(format_number $mem_total)K ($mem_percent%)"
}

# Function to get disk usage
get_disk_usage() {
    # Get disk usage for root filesystem
    df -h / | grep -v Filesystem | awk '{print $3 " / " $2 " (" $5 ")"}'
}

# Function to get network stats
get_network_stats() {
    # Get received and transmitted bytes
    local rx_bytes=$(cat /proc/net/dev | grep eth0 | awk '{print $2}')
    local tx_bytes=$(cat /proc/net/dev | grep eth0 | awk '{print $10}')
    
    # If eth0 doesn't exist, try other common interface names
    if [[ -z "$rx_bytes" ]]; then
        rx_bytes=$(cat /proc/net/dev | grep ens | head -1 | awk '{print $2}')
        tx_bytes=$(cat /proc/net/dev | grep ens | head -1 | awk '{print $10}')
    fi
    
    # If still no interface found, use lo as fallback
    if [[ -z "$rx_bytes" ]]; then
        rx_bytes=$(cat /proc/net/dev | grep lo | awk '{print $2}')
        tx_bytes=$(cat /proc/net/dev | grep lo | awk '{print $10}')
    fi
    
    echo "RX: $(format_number $((rx_bytes/1024)))K, TX: $(format_number $((tx_bytes/1024)))K"
}

# Function to print a horizontal line
print_line() {
    printf '%*s\n' "${COLUMNS:-$(tput cols)}" '' | tr ' ' '-'
}

# Print header
print_line
printf "%-25s %-20s %-20s %-20s %-20s\n" "TIMESTAMP" "CPU USAGE" "MEMORY USAGE" "DISK USAGE" "NETWORK"
print_line

# Main monitoring loop
for ((i=1; i<=count; i++)); do
    # Get current timestamp
    timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    
    # Get system statistics
    cpu=$(get_cpu_usage)
    memory=$(get_memory_usage)
    disk=$(get_disk_usage)
    network=$(get_network_stats)
    
    # Print statistics
    printf "%-25s %-20s %-20s %-20s %-20s\n" \
        "$timestamp" "$cpu" "$memory" "$disk" "$network"
    
    # Exit if this is the last iteration
    if ((i == count)); then
        break
    fi
    
    # Wait for the specified interval
    sleep "$interval"
done

print_line
echo "Monitoring complete. Checked system $count times at ${interval}-second intervals."
exit 0
```

### Backup Script

```bash
#!/bin/bash
#
# backup.sh - A script to backup important files
#
# Usage: ./backup.sh [source_dir] [destination_dir]
#
# If arguments are not provided, the script will use
# the current directory as source and create a backup
# in the parent directory.

# Set safe bash options
set -euo pipefail

# Function to display script usage
usage() {
    echo "Usage: $0 [source_dir] [destination_dir]"
    echo "  source_dir: Directory to backup (default: current directory)"
    echo "  destination_dir: Backup destination (default: parent directory)"
}

# Function to log messages
log() {
    echo "[$(date "+%Y-%m-%d %H:%M:%S")] $1"
}

# Function to check if directory exists
check_directory() {
    if [ ! -d "$1" ]; then
        log "Error: Directory '$1' does not exist."
        exit 1
    fi
}

# Function to calculate directory size
get_directory_size() {
    du -sh "$1" | cut -f1
}

# Process command-line arguments
source_dir="${1:-.}"  # Default to current directory if not specified
dest_dir="${2:-..}"   # Default to parent directory if not specified

# Check if source directory exists
check_directory "$source_dir"

# Check if destination directory exists
check_directory "$dest_dir"

# Convert to absolute paths
source_dir=$(realpath "$source_dir")
dest_dir=$(realpath "$dest_dir")

# Get source directory name (for the backup file name)
source_name=$(basename "$source_dir")

# Create timestamp for the backup file
timestamp=$(date "+%Y%m%d-%H%M%S")
backup_file="${dest_dir}/${source_name}-backup-${timestamp}.tar.gz"

# Calculate source directory size
log "Calculating size of '$source_dir'..."
dir_size=$(get_directory_size "$source_dir")
log "Directory size: $dir_size"

# Ask for confirmation
read -p "Create backup of '$source_dir' to '$backup_file'? (y/n): " confirm
if [[ "$confirm" != [yY]* ]]; then
    log "Backup cancelled."
    exit 0
fi

# Create the backup
log "Creating backup..."
tar -czf "$backup_file" -C "$(dirname "$source_dir")" "$(basename "$source_dir")" 2>/dev/null || {
    log "Error: Backup failed."
    exit 1
}

# Verify the backup was created
if [ -f "$backup_file" ]; then
    backup_size=$(du -h "$backup_file" | cut -f1)
    log "Backup created successfully: '$backup_file' (Size: $backup_size)"
else
    log "Error: Backup file was not created."
    exit 1
fi

# List files in the backup (optional)
read -p "List files in the backup? (y/n): " list_files
if [[ "$list_files" == [yY]* ]]; then
    log "Files in the backup:"
    tar -tzf "$backup_file" | head -20  # Show only first 20 files
    
    file_count=$(tar -tzf "$backup_file" | wc -l)
    if [ "$file_count" -gt 20 ]; then
        echo "... and $((file_count - 20)) more files"
    fi
fi

# Suggest creating a checksum
read -p "Create SHA256 checksum of the backup? (y/n): " create_checksum
if [[ "$create_checksum" == [yY]* ]]; then
    checksum_file="${backup_file}.sha256"
    sha256sum "$backup_file" > "$checksum_file"
    log "Checksum created: '$checksum_file'"
    cat "$checksum_file"
fi

log "Backup process completed."
exit 0
```

### Log Analyzer

```bash
#!/bin/bash
#
# log_analyzer.sh - Analyze log files and generate a summary report
#
# Usage: ./log_analyzer.sh [log_file] [output_file]
#
# If arguments are not provided, the script will look for access.log
# in the current directory and output to log_report.txt

# Set safe bash options
set -euo pipefail

# Default values
log_file="${1:-access.log}"
output_file="${2:-log_report.txt}"

# Check if log file exists
if [ ! -f "$log_file" ]; then
    echo "Error: Log file '$log_file' not found."
    exit 1
fi

# Function to print section headers
print_header() {
    echo
    echo "==== $1 ===="
    echo
}

# Create the report file
echo "Log Analysis Report" > "$output_file"
echo "Generated on $(date)" >> "$output_file"
echo "Analyzing: $log_file" >> "$output_file"
echo "----------------------------------------" >> "$output_file"

# Count total number of log entries
total_lines=$(wc -l < "$log_file")
echo "Total log entries: $total_lines" >> "$output_file"

# Add basic statistics
print_header "Basic Statistics" >> "$output_file"

# Count HTTP status codes
echo "HTTP Status Code Distribution:" >> "$output_file"
grep -o ' [0-9]\{3\} ' "$log_file" | sort | uniq -c | sort -nr | while read -r count code; do
    # Determine status code description
    case ${code# } in
        200) description="OK" ;;
        201) description="Created" ;;
        301) description="Moved Permanently" ;;
        302) description="Found (Redirect)" ;;
        304) description="Not Modified" ;;
        400) description="Bad Request" ;;
        401) description="Unauthorized" ;;
        403) description="Forbidden" ;;
        404) description="Not Found" ;;
        500) description="Internal Server Error" ;;
        503) description="Service Unavailable" ;;
        *)   description="Other" ;;
    esac
    
    percentage=$(awk "BEGIN {printf \"%.2f\", ($count / $total_lines) * 100}")
    printf "  %s: %s (%s%%) - %s\n" "${code# }" "$count" "$percentage" "$description" >> "$output_file"
done

# Get top IP addresses
print_header "Top 10 IP Addresses" >> "$output_file"
grep -o '^[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}' "$log_file" | sort | uniq -c | sort -nr | head -10 | while read -r count ip; do
    percentage=$(awk "BEGIN {printf \"%.2f\", ($count / $total_lines) * 100}")
    printf "  %s: %s (%s%%)\n" "$ip" "$count" "$percentage" >> "$output_file"
done

# Get most requested pages
print_header "Top 10 Requested Pages" >> "$output_file"
grep -o "GET [^ ]*" "$log_file" | sed 's/GET //' | sort | uniq -c | sort -nr | head -10 | while read -r count page; do
    percentage=$(awk "BEGIN {printf \"%.2f\", ($count / $total_lines) * 100}")
    printf "  %s: %s (%s%%)\n" "$page" "$count" "$percentage" >> "$output_file"
done

# Get most common user agents
print_header "Top 5 User Agents" >> "$output_file"
grep -o '"Mozilla[^"]*' "$log_file" | sort | uniq -c | sort -nr | head -5 | while read -r count agent; do
    printf "  %s: %s\n" "${agent#\"}" "$count" >> "$output_file"
done

# Analyze traffic by hour
print_header "Traffic by Hour" >> "$output_file"
grep -o "[0-9]\{2\}/[A-Za-z]\{3\}/[0-9]\{4\}:[0-9]\{2\}" "$log_file" | cut -d: -f2 | sort | uniq -c | sort -k2 -n | while read -r count hour; do
    percentage=$(awk "BEGIN {printf \"%.2f\", ($count / $total_lines) * 100}")
    printf "  %s:00 - %s:59: %s (%s%%)\n" "$hour" "$hour" "$count" "$percentage" >> "$output_file"
done

# Find error logs
print_header "Recent Error Logs (HTTP 4xx or 5xx)" >> "$output_file"
grep ' [45][0-9][0-9] ' "$log_file" | tail -10 >> "$output_file"

# Calculate average response size (if available in the logs)
if grep -q ' [0-9]\+ $' "$log_file"; then
    print_header "Response Size Statistics" >> "$output_file"
    total_size=$(grep -o ' [0-9]\+ $' "$log_file" | awk '{sum += $1} END {print sum}')
    average_size=$(grep -o ' [0-9]\+ $' "$log_file" | awk '{sum += $1; count++} END {printf "%.2f", sum/count}')
    max_size=$(grep -o ' [0-9]\+ $' "$log_file" | sort -nr | head -1)
    
    echo "Total data transferred: $(numfmt --to=iec-i --suffix=B $total_size)" >> "$output_file"
    echo "Average response size: $(numfmt --to=iec-i --suffix=B $average_size)" >> "$output_file"
    echo "Maximum response size: $(numfmt --to=iec-i --suffix=B $max_size)" >> "$output_file"
fi

# Add summary
print_header "Summary" >> "$output_file"
echo "Log analysis complete. The report contains:" >> "$output_file"
echo "- Overview of HTTP status codes" >> "$output_file"
echo "- Top IP addresses accessing the server" >> "$output_file"
echo "- Most requested pages" >> "$output_file"
echo "- Most common user agents" >> "$output_file"
echo "- Traffic distribution by hour" >> "$output_file"
echo "- Recent error logs" >> "$output_file"
if grep -q ' [0-9]\+ $' "$log_file"; then
    echo "- Response size statistics" >> "$output_file"
fi

echo "Analysis completed. Report saved to '$output_file'"
exit 0
```

## Advanced Topics

### Process Management

```bash
#!/bin/bash

# Start a background process
sleep 100 &
bg_pid=$!  # Store the process ID of the last background command
echo "Background process started with PID: $bg_pid"

# Check if a process is running
if ps -p "$bg_pid" > /dev/null; then
    echo "Process $bg_pid is running"
else
    echo "Process $bg_pid is not running"
fi

# Kill a process
kill "$bg_pid"
echo "Sent SIGTERM to process $bg_pid"

# Wait for a background process to complete
wait "$bg_pid" 2>/dev/null || echo "Process $bg_pid terminated"

# Start multiple background processes
for i in {1..3}; do
    (
        echo "Starting worker $i"
        sleep $((i * 2))
        echo "Worker $i completed"
    ) &
    pids[$i]=$!
done

# Wait for all background processes to complete
for pid in "${pids[@]}"; do
    wait "$pid"
done
echo "All workers completed"

# Run a command with a timeout
timeout 5s ping -c 10 localhost
echo "Exit status: $?"  # 124 if timed out, command's exit status otherwise

# CPU and memory limits using ulimit
ulimit -t 10  # CPU time limit in seconds
ulimit -v 1000000  # Virtual memory limit in KB
```

### Signal Handling

```bash
#!/bin/bash

# Function to handle SIGINT signal (Ctrl+C)
handle_sigint() {
    echo
    echo "SIGINT (Ctrl+C) received. Cleaning up..."
    # Cleanup actions here
    echo "Done!"
    exit 1
}

# Function to handle SIGTERM signal
handle_sigterm() {
    echo "SIGTERM received. Gracefully shutting down..."
    # Graceful shutdown actions here
    echo "Done!"
    exit 0
}

# Set up signal handlers
trap handle_sigint SIGINT
trap handle_sigterm SIGTERM
trap "echo 'Script is exiting...'" EXIT

# Main program loop
echo "Script running with PID $$"
echo "Press Ctrl+C to send SIGINT."
echo "From another terminal, try: kill $$ (to send SIGTERM)"

count=0
while true; do
    echo -ne "Running for $count seconds\r"
    sleep 1
    ((count++))
done
```

### Parallel Processing

```bash
#!/bin/bash

# Simple parallel processing using &
parallel_process() {
    echo "Starting task $1 (will take $2 seconds)"
    sleep "$2"
    echo "Task $1 completed"
}

echo "Starting parallel tasks..."

# Start multiple tasks in parallel
for i in {1..5}; do
    parallel_process "$i" "$((i * 2))" &
done

# Wait for all background jobs to complete
wait
echo "All tasks completed"

# GNU Parallel usage (if installed)
if command -v parallel >/dev/null 2>&1; then
    echo "Using GNU Parallel for better parallelism"
    
    # Process files in parallel (4 jobs at a time)
    find . -name "*.txt" | parallel -j 4 "echo Processing {}; wc -l {}"
    
    # Parallel processing with arguments
    parallel -j 3 parallel_process {1} {2} ::: 1 2 3 ::: 5 3 1
fi
```

### Templates and Here Documents

```bash
#!/bin/bash

# Generate a configuration file using a here document
config_file="config.ini"
user_name="admin"
port=8080
debug_mode="true"

cat > "$config_file" << EOF
# Configuration file generated by $(basename "$0")
# Date: $(date)

[Server]
Username = $user_name
Port = $port
Debug = $debug_mode

[Paths]
LogDir = /var/log/app
DataDir = /var/data/app
EOF

echo "Created configuration file: $config_file"

# Create an HTML file with a template
generate_html_report() {
    local title="$1"
    local content="$2"
    local output_file="$3"
    
    cat > "$output_file" << HTML
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>$title</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        h1 { color: #333; }
        .content { border: 1px solid #ddd; padding: 20px; }
        .footer { margin-top: 30px; font-size: 0.8em; color: #777; }
    </style>
</head>
<body>
    <h1>$title</h1>
    <div class="content">
$content
    </div>
    <div class="footer">
        Generated on $(date) by $(whoami)
    </div>
</body>
</html>
HTML

    echo "Created HTML report: $output_file"
}

# Generate a sample report
report_content="
<p>This is a sample report with <b>formatted</b> content.</p>
<ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>
"

generate_html_report "Sample Report" "$report_content" "report.html"
```

### Advanced Text Processing with awk

```bash
#!/bin/bash

# Create a sample CSV file for demonstration
cat > data.csv << EOF
Name,Department,Salary,Start Date
John Smith,Engineering,75000,2018-05-15
Mary Johnson,Marketing,82000,2017-03-01
Bob Williams,Engineering,65000,2020-01-10
Alice Brown,Finance,90000,2015-11-22
Charlie Davis,Marketing,67500,2019-08-05
Eva Wilson,Engineering,80000,2016-04-30
EOF

echo "Created sample data file: data.csv"

# Basic awk usage to extract specific columns
echo -e "\nDisplaying Names and Salaries:"
awk -F, 'NR>1 {print $1 ": $" $3}' data.csv

# Calculate average salary
echo -e "\nAverage Salary Calculation:"
awk -F, '
    NR>1 {
        sum += $3
        count++
    }
    END {
        printf "Average Salary: $%.2f\n", sum/count
    }
' data.csv

# Group by department and calculate department averages
echo -e "\nDepartment Salary Analysis:"
awk -F, '
    NR>1 {
        dept[$2] += $3
        count[$2]++
    }
    END {
        print "Department, Average Salary, Total Employees"
        for (d in dept) {
            printf "%s, $%.2f, %d\n", d, dept[d]/count[d], count[d]
        }
    }
' data.csv

# Calculate years of service based on start date
echo -e "\nEmployee Service Duration:"
awk -F, '
    function calculate_years(start_date) {
        split(start_date, date_parts, "-")
        start_year = date_parts[1]
        start_month = date_parts[2]
        start_day = date_parts[3]
        
        current_year = strftime("%Y")
        current_month = strftime("%m")
        current_day = strftime("%d")
        
        years = current_year - start_year
        
        if ((current_month < start_month) || 
            (current_month == start_month && current_day < start_day)) {
            years--
        }
        
        return years
    }
    
    NR>1 {
        years = calculate_years($4)
        printf "%s has worked for %d years\n", $1, years
    }
' data.csv

# Format output as an HTML table
echo -e "\nGenerating HTML Table:"
awk -F, '
    BEGIN {
        print "<table border=\"1\">"
        print "<tr><th>Name</th><th>Department</th><th>Salary</th><th>Start Date</th></tr>"
    }
    NR>1 {
        print "<tr>"
        print "<td>" $1 "</td>"
        print "<td>" $2 "</td>"
        print "<td>$" $3 "</td>"
        print "<td>" $4 "</td>"
        print "</tr>"
    }
    END {
        print "</table>"
    }
' data.csv > employee_table.html

echo "Generated HTML table: employee_table.html"
```

## Resources for Further Learning

### Bash Analysis and Linting Tools

Static analysis tools help identify potential issues in your scripts before they cause problems in production. Here's a comparison of the most useful Bash script analysis tools:

| Tool | Description | Best For |
|------|-------------|----------|
| **ShellCheck** | The most popular static analysis tool for shell scripts. Identifies syntax issues, semantic problems, and potential bugs. | General-purpose shell script validation; beginners and experts alike |
| **Bashate** | A code style enforcement tool inspired by Python's PEP8. Focuses on formatting and style consistency. | Teams that want consistent formatting across all scripts |
| **i-Code CNES** | Open-source static code analyzer supporting both Shell and Fortran scripts. Developed by the French Space Agency. | Scientific computing environments; complex validation requirements |
| **Shellharden** | Focuses on syntax highlighting and semi-automated rewriting of scripts to make them safer. Specializes in proper quoting. | Hardening existing scripts against common security issues |
| **Checkbashisms** | Specifically detects "bashisms" (Bash-specific features) in scripts intended to be POSIX-compliant. | Writing portable scripts that need to run on multiple shells |
| **Trunk Check** | A meta-linter that can integrate ShellCheck and other tools into a unified workflow. | Projects with multiple languages and tools |
| **Mega-Linter** | A combination of multiple linters that can be installed as a GitHub Action. | CI/CD pipelines; multi-language projects |

#### Editor Integrations

Most of these tools can be integrated with popular code editors:

- **Flycheck**: For Emacs users, provides real-time syntax checking
- **SublimeLinter**: Linting plugin for Sublime Text, supports multiple linters
- **Linter**: Package for Atom/Pulsar Editor with shell script support

#### Example ShellCheck Integration

Here's how to use ShellCheck from the command line:

```bash
# Install ShellCheck (Ubuntu/Debian)
sudo apt-get install shellcheck

# Check a script
shellcheck myscript.sh

# Enable specific checks and disable others
shellcheck -e SC2034,SC2154 --enable=all myscript.sh
```

For continuous integration, add to your CI config:

```yaml
# Example GitHub Actions workflow
name: ShellCheck

on: [push, pull_request]

jobs:
  shellcheck:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Run ShellCheck
      uses: ludeeus/action-shellcheck@master
```

#### Integrating Bash and Python Analysis Tools

For projects that use both Bash and Python, you can set up an integrated analysis workflow:

1. **Pre-commit Hooks**: Set up pre-commit to run both Bash and Python linters before each commit:

```yaml
# .pre-commit-config.yaml
repos:
-   repo: https://github.com/koalaman/shellcheck-precommit
    rev: v0.9.0
    hooks:
    -   id: shellcheck
-   repo: https://github.com/pycqa/flake8
    rev: 6.0.0
    hooks:
    -   id: flake8
-   repo: https://github.com/pycqa/isort
    rev: 5.12.0
    hooks:
    -   id: isort
-   repo: https://github.com/psf/black
    rev: 23.3.0
    hooks:
    -   id: black
```

2. **VS Code Configuration**: Configure VS Code to work with both languages:

```json
// .vscode/settings.json
{
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true,
    "shellcheck.enable": true,
    "shellcheck.useWorkspaceRootAsCwd": true,
    "shellcheck.run": "onSave"
}
```

3. **Cross-Language Tools**:

- **SonarQube**: Can analyze both Bash and Python code in the same project
- **Mega-Linter**: Supports 50+ languages including Bash and Python
- **Trunk**: Unified linting for both Bash and Python scripts

4. **CI Pipeline Example** (GitHub Actions for both languages):

```yaml
name: Lint

on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'
    
    - name: Install Python dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8 black isort
    
    - name: Run Python linters
      run: |
        flake8 .
        black --check .
        isort --check .
    
    - name: Run ShellCheck
      uses: ludeeus/action-shellcheck@master
      with:
        scandir: './scripts'
```

5. **Python-Bash Interaction Analysis**:

- **pyshellcheck**: A Python wrapper for ShellCheck that can be used in Python scripts that generate or analyze Bash code
- **bashlex**: A Python library for parsing Bash code, useful for custom static analysis tools
- **explainshell**: A web tool that helps understand shell commands (can be used via API)

This integrated approach ensures code quality across both languages, especially important when Bash and Python scripts interact with each other in the same project.

Integrating these tools into your development workflow helps catch errors early and ensures consistent, high-quality code across both Bash and Python.

### Books

1. **"Learning the bash Shell"** by Cameron Newham
2. **"bash Cookbook"** by Carl Albing, JP Vossen, and Cameron Newham
3. **"The Linux Command Line"** by William Shotts
4. **"Mastering Unix Shell Scripting"** by Randal K. Michael

### Python Resources for System Scripting

1. **Documentation**:
   - [Python Standard Library Documentation](https://docs.python.org/3/library/index.html)
   - [subprocess module](https://docs.python.org/3/library/subprocess.html)
   - [os module](https://docs.python.org/3/library/os.html)
   - [pathlib module](https://docs.python.org/3/library/pathlib.html)

2. **Books and Tutorials**:
   - **"Automate the Boring Stuff with Python"** by Al Sweigart
   - **"Python for Unix and Linux System Administration"** by Noah Gift and Jeremy Jones

3. **Libraries for System Scripting**:
   - [Click](https://click.palletsprojects.com/) - Command line interface creation tool
   - [Paramiko](http://www.paramiko.org/) - SSH protocol library
   - [Fabric](http://www.fabfile.org/) - Streamlined remote execution and deployment
   - [PyYAML](https://pyyaml.org/) - YAML parser and emitter

### Community Resources

1. **Forums and Q&A Sites**:
   - [Stack Overflow](https://stackoverflow.com/questions/tagged/bash)
   - [Unix & Linux Stack Exchange](https://unix.stackexchange.com/)
   - [Reddit r/bash](https://www.reddit.com/r/bash/)

2. **Cheat Sheets**:
   - [Bash Cheat Sheet](https://devhints.io/bash)
   - [Python Cheat Sheet](https://www.pythoncheatsheet.org/)
