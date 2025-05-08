# Advanced Bash Scripting Guide

## Table of Contents
- [Advanced Bash Techniques](#advanced-bash-techniques)
- [Regular Expressions in Depth](#regular-expressions-in-depth)
- [Parameter Expansion and Manipulation](#parameter-expansion-and-manipulation)
- [Process Substitution](#process-substitution)
- [Advanced Loop Techniques](#advanced-loop-techniques)
- [Error Handling and Debugging](#error-handling-and-debugging)
- [Security Best Practices](#security-best-practices)
- [Performance Optimization](#performance-optimization)
- [Advanced Text Processing](#advanced-text-processing)
- [Bash Networking](#bash-networking)
- [File Descriptors and Redirection](#file-descriptors-and-redirection)
- [Subshells and Coprocesses](#subshells-and-coprocesses)
- [Bash Arrays and Associative Arrays](#bash-arrays-and-associative-arrays)
- [Bash Completion](#bash-completion)
- [Real-World Scripts](#real-world-scripts)

## Advanced Bash Techniques

### Strict Mode

```bash
#!/usr/bin/env bash

# Strict mode - makes bash less forgiving of errors
set -euo pipefail
IFS=$'\n\t'

# -e: Exit immediately if a command exits with non-zero status
# -u: Treat unset variables as an error
# -o pipefail: Return value of a pipeline is the value of the last command to exit with non-zero status
# IFS: Internal Field Separator (restrict to newlines and tabs)
```

### Defensive Programming

```bash
#!/usr/bin/env bash

# Check for required commands
command -v jq >/dev/null 2>&1 || { echo >&2 "jq is required but not installed. Aborting."; exit 1; }

# Default values
timeout=${TIMEOUT:-30}
retries=${RETRIES:-3}
host=${HOST:-"localhost"}

# Input validation
is_number() {
    [[ $1 =~ ^[0-9]+$ ]] || return 1
    return 0
}

if ! is_number "$timeout"; then
    echo "Error: timeout must be a number" >&2
    exit 1
fi

# Safe temporary files
temp_file=$(mktemp)
trap 'rm -f "$temp_file"' EXIT

# Function arguments with defaults
function fetch_data() {
    local url="${1:-http://example.com}"
    local timeout="${2:-5}"
    
    curl --max-time "$timeout" "$url" 2>/dev/null || echo "Error: Failed to fetch $url"
}
```

### Process Control and Job Management

```bash
#!/usr/bin/env bash

# Start background jobs
for i in {1..5}; do
    (
        echo "Job $i starting"
        sleep $((RANDOM % 10 + 1))
        echo "Job $i completed"
    ) &
    pids[$i]=$!
done

# Different ways to wait for jobs
wait_all() {
    # Wait for all background jobs
    wait
}

wait_specific() {
    # Wait for a specific job
    wait "${pids[3]}" && echo "Job 3 finished"
}

wait_any() {
    # Wait for any job to complete
    wait -n && echo "A job has completed"
}

# Check job status
for pid in "${pids[@]}"; do
    if kill -0 "$pid" 2>/dev/null; then
        echo "Process $pid is still running"
    fi
done

# Limit concurrent jobs
max_jobs=3
run_with_limit() {
    while [[ $(jobs -r | wc -l) -ge $max_jobs ]]; do
        sleep 1
    done
    "$@" &
}

# Demonstrate job control
for url in "${urls[@]}"; do
    run_with_limit curl -s "$url" > "$url.html"
done
```

## Regular Expressions in Depth

### Regex Pattern Elements

| Element | Description | Example | Matches |
|---------|-------------|---------|---------|
| `.` | Matches any single character | `a.c` | "abc", "a1c", "a c", etc. |
| `^` | Matches start of string | `^hello` | "hello world" but not "world hello" |
| `$` | Matches end of string | `world$` | "hello world" but not "world hello" |
| `*` | Matches 0+ occurrences | `a*b` | "b", "ab", "aab", "aaab", etc. |
| `+` | Matches 1+ occurrences | `a+b` | "ab", "aab", "aaab", but not "b" |
| `?` | Matches 0 or 1 occurrence | `a?b` | "b", "ab", but not "aab" |
| `{n}` | Matches exactly n occurrences | `a{3}` | "aaa" |
| `{n,}` | Matches n or more occurrences | `a{2,}` | "aa", "aaa", "aaaa", etc. |
| `{n,m}` | Matches between n and m occurrences | `a{2,4}` | "aa", "aaa", "aaaa" |
| `|` | Alternation (OR) | `cat\|dog` | "cat", "dog" |
| `[...]` | Character class | `[aeiou]` | any vowel |
| `[^...]` | Negated character class | `[^aeiou]` | any character except vowels |
| `\d` | Digit | `\d{3}` | "123", "456", etc. |
| `\w` | Word character | `\w+` | "hello", "abc123", etc. |
| `\s` | Whitespace | `a\sb` | "a b" |
| `\b` | Word boundary | `\bcat\b` | "cat" as a whole word |
| `(...)` | Capturing group | `(ab)+` | "ab", "abab", "ababab", etc. |
| `(?:...)` | Non-capturing group | `(?:ab)+` | Same as above but doesn't create backreference |

### Regex in Bash Examples

```bash
#!/usr/bin/env bash

# Basic pattern matching with =~
if [[ "abc123" =~ ^[a-z]+[0-9]+$ ]]; then
    echo "String matches pattern (letters followed by numbers)"
fi

# Using capture groups
email="user@example.com"
if [[ $email =~ ([a-zA-Z0-9._%+-]+)@([a-zA-Z0-9.-]+\.[a-zA-Z]{2,}) ]]; then
    username="${BASH_REMATCH[1]}"
    domain="${BASH_REMATCH[2]}"
    echo "Username: $username"
    echo "Domain: $domain"
fi

# Validating IP addresses
validate_ip() {
    local ip=$1
    local ip_regex='^([0-9]{1,3}\.){3}[0-9]{1,3}$'
    
    if [[ ! $ip =~ $ip_regex ]]; then
        return 1
    fi
    
    IFS='.' read -ra octets <<< "$ip"
    for octet in "${octets[@]}"; do
        if (( octet > 255 )); then
            return 1
        fi
    done
    
    return 0
}

# Extract version from string
extract_version() {
    local input=$1
    local version_regex='version ([0-9]+\.[0-9]+\.[0-9]+)'
    
    if [[ $input =~ $version_regex ]]; then
        echo "${BASH_REMATCH[1]}"
    else
        echo "No version found"
    fi
}

version=$(extract_version "This is version 1.2.34 of the software")
echo "Extracted version: $version"
```

### Regex in Grep, Sed, and Awk

```bash
#!/usr/bin/env bash

# Grep examples
echo "Finding email addresses in file:"
grep -E '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}' contacts.txt

# Case-insensitive matching
grep -i 'error' logs.txt

# Using capture groups with grep -P (PCRE)
grep -P '(?<=Name: ).+' user_data.txt  # Positive lookbehind

# Sed examples
echo "Replacing phone numbers with masked format:"
sed -E 's/([0-9]{3})([0-9]{3})([0-9]{4})/\1-\2-\3/g' data.txt

# Multiple sed operations
sed -e 's/old/new/g' -e 's/bad/good/g' file.txt

# Conditional replacement
sed '/^#/!s/foo/bar/g' config.txt  # Replace foo with bar only in non-comment lines

# Awk with regex
echo "Extracting data between tags:"
awk '/<data>/{flag=1; next} /<\/data>/{flag=0} flag' xml_file.txt

# Multiple pattern matching
awk '/pattern1/ || /pattern2/ {print}' file.txt

# Using regex for field splitting
echo "Parsing complex CSV:"
awk -F'[;,]' '{print $1, $3}' complex_data.csv
```

## Parameter Expansion and Manipulation

### Basic Parameter Expansion

| Syntax | Description | Example | Result |
|--------|-------------|---------|--------|
| `${var}` | Basic expansion | `name="John"; echo ${name}` | "John" |
| `${var:-default}` | Use default if var is unset or null | `echo ${name:-Unknown}` | "John" or "Unknown" if unset |
| `${var:=default}` | Set default if var is unset or null | `unset name; echo ${name:=Unknown}` | Sets and returns "Unknown" |
| `${var:?error}` | Display error if var is unset or null | `echo ${name:?"Name is required"}` | Error if unset |
| `${var:+value}` | Use alternate value if var is set | `echo ${name:+Mr. $name}` | "Mr. John" or "" if unset |
| `${#var}` | String length | `echo ${#name}` | "4" for "John" |
| `${var:offset}` | Substring from offset | `echo ${name:1}` | "ohn" |
| `${var:offset:length}` | Substring with length | `echo ${name:1:2}` | "oh" |

### Advanced Parameter Manipulation

```bash
#!/usr/bin/env bash

# String manipulation
filename="script.txt.bak"

# Remove smallest suffix pattern
echo "${filename%.bak}"  # script.txt

# Remove largest suffix pattern
echo "${filename%%.*}"   # script

# Remove smallest prefix pattern
echo "${filename#*.}"    # txt.bak

# Remove largest prefix pattern
echo "${filename##*.}"   # bak

# Pattern replacement
path="/usr/local/bin:/usr/bin:/bin"

# Replace first match
echo "${path/bin/lib}"   # /usr/local/lib:/usr/bin:/bin

# Replace all matches
echo "${path//bin/lib}"  # /usr/local/lib:/usr/lib:/lib

# Replace prefix
echo "${path/#\/usr/\/opt}"  # /opt/local/bin:/usr/bin:/bin

# Replace suffix
echo "${path/%bin/lib}"  # /usr/local/bin:/usr/bin:/lib

# Case modification
name="John Doe"

# Uppercase first character
echo "${name^}"         # John Doe (no change if already uppercase)
echo "${name,}"         # john Doe (lowercase first character)

# Uppercase all characters
echo "${name^^}"        # JOHN DOE
echo "${name,,}"        # john doe (lowercase all)

# Selective case modification
echo "${name^j}"        # John Doe (uppercase only 'j')
echo "${name^^[jd]}"    # John Doe (uppercase only 'j' and 'd')

# Variable indirection
fruit="apple"
apple_count=5
orange_count=10

# Get variable value by name
echo "${!fruit}_count"  # 5 (value of apple_count)

# Array of variable names starting with a prefix
fruits=("apple" "orange" "banana")
prefix="fr"
echo "${!prefix*}"  # fruits (variable names starting with "fr")
```

### Parameter Expansion for Arrays

```bash
#!/usr/bin/env bash

# Array operations
fruits=("apple" "banana" "cherry" "date")

# Length of array
echo "${#fruits[@]}"  # 4

# Length of specific element
echo "${#fruits[1]}"  # 6 (length of "banana")

# Slice of array
echo "${fruits[@]:1:2}"  # banana cherry

# Join array elements
IFS=", "
echo "${fruits[*]}"  # apple, banana, cherry, date
unset IFS

# Convert array to string and back
array_str="${fruits[@]}"
eval "reconstructed_array=($array_str)"
echo "${reconstructed_array[2]}"  # cherry

# Multiple delimiter substitution
data="field1:field2,field3;field4"
data="${data//[:;,]/ }"
echo "$data"  # field1 field2 field3 field4

# Using built-in variables
echo "Number of arguments: $#"
echo "All arguments: $@"
echo "Script name: ${0##*/}"
echo "Last return code: $?"
```

## Process Substitution

```bash
#!/usr/bin/env bash

# Basic process substitution
diff <(ls -la) <(ls -la /tmp)

# Multiple command process substitution
while read -r line; do
    echo "Line: $line"
done < <(grep "ERROR" log.txt | sort | uniq)

# Using process substitution with command chains
grep "pattern" <(zcat large_file.gz) | awk '{print $2}'

# Process substitution for temporary files
sed 's/old/new/g' < <(cat file1.txt file2.txt) > combined.txt

# Process substitution with loops
for file in *.txt; do
    if cmp -s <(sort "$file") <(sort -r "$file" | tac); then
        echo "$file is a palindrome file"
    fi
done

# Process substitution for function output
compare_files() {
    local f1=$1
    local f2=$2
    
    comm -3 <(sort "$f1") <(sort "$f2")
}

# Process substitution with awk
awk 'NR==FNR {a[$1]=$2; next} $1 in a {print $1, a[$1], $2}' \
    <(cut -f1,2 file1.tsv) \
    <(cut -f1,3 file2.tsv)

# Real-world example: compare directories
find_differences() {
    local dir1=$1
    local dir2=$2
    
    diff -y --suppress-common-lines \
        <(find "$dir1" -type f -exec md5sum {} \; | sort) \
        <(find "$dir2" -type f -exec md5sum {} \; | sort)
}
```

## Advanced Loop Techniques

### Loop with Named Pipes

```bash
#!/usr/bin/env bash

# Create a named pipe
pipe=$(mktemp -u)
mkfifo "$pipe"
trap 'rm -f "$pipe"' EXIT

# Producer
{
    for i in {1..10}; do
        echo "Data $i"
        sleep 0.5
    done
    echo "EOF"
} > "$pipe" &

# Consumer
while read -r line; do
    if [[ "$line" == "EOF" ]]; then
        break
    fi
    echo "Processed: $line"
done < "$pipe"
```

### Complex Iteration Patterns

```bash
#!/usr/bin/env bash

# Multi-dimensional iteration
for x in {1..3}; do
    for y in {A..C}; do
        echo "Coordinate ($x,$y)"
    done
done

# Skipping and early termination
for i in {1..20}; do
    # Skip even numbers
    (( i % 2 == 0 )) && continue
    
    # Exit loop early
    (( i > 15 )) && break
    
    echo "Processing $i"
done

# Iterating with step value
for ((i=0; i<=20; i+=5)); do
    echo "Step value: $i"
done

# Iterate over two arrays in parallel
names=("Alice" "Bob" "Charlie")
ages=(25 32 45)

for ((i=0; i<${#names[@]}; i++)); do
    echo "${names[i]} is ${ages[i]} years old"
done

# Using brace expansion for complex sequences
for prefix in {A..C}; do
    for suffix in {1..3}{a..c}; do
        echo "$prefix-$suffix"
    done
done

# Nested loop with labels
outer_loop() {
    local i=0
    while (( i < 10 )); do
        local j=0
        while (( j < 10 )); do
            if (( i * j > 50 )); then
                echo "Breaking from nested loops at i=$i, j=$j"
                return
            fi
            (( j++ ))
        done
        (( i++ ))
    done
}
```

### Parallel Processing Patterns

```bash
#!/usr/bin/env bash

# Basic parallel processing
parallel_process() {
    local max_jobs=5
    local running=0
    
    for item in "$@"; do
        # Limit the number of parallel jobs
        if (( running >= max_jobs )); then
            wait -n  # Wait for any child to finish
            (( running-- ))
        fi
        
        # Start background job
        (
            echo "Processing $item"
            sleep $((RANDOM % 5 + 1))
            echo "Finished $item"
        ) &
        
        (( running++ ))
    done
    
    # Wait for remaining jobs
    wait
}

# Run parallel jobs with results collection
process_urls() {
    local result_file=$(mktemp)
    trap 'rm -f "$result_file"' EXIT
    
    for url in "$@"; do
        {
            local status=$(curl -s -o /dev/null -w "%{http_code}" "$url")
            echo "$url $status" >> "$result_file"
        } &
    done
    
    wait
    sort "$result_file"
}
```

## Error Handling and Debugging

### Advanced Error Trapping

```bash
#!/usr/bin/env bash

# Set strict mode
set -euo pipefail

# Global error handler
error_handler() {
    local line=$1
    local cmd=$2
    local code=$3
    echo "Error on line $line, running command '$cmd' (exit code: $code)" >&2
    
    # Optional: Send error notification
    # mail -s "Script Error" admin@example.com <<< "Error in script at line $line"
    
    # Clean up resources
    cleanup
    exit $code
}

cleanup() {
    echo "Performing cleanup..."
    # Delete temporary files, close connections, etc.
}

# Set error trap
trap 'error_handler $LINENO "$BASH_COMMAND" $?' ERR

# Set exit trap for cleanup
trap cleanup EXIT

# Function with custom error handling
run_with_retry() {
    local cmd=$1
    local max_attempts=${2:-3}
    local attempt=1
    
    while (( attempt <= max_attempts )); do
        echo "Attempt $attempt of $max_attempts: $cmd"
        
        if eval "$cmd"; then
            return 0
        else
            local exit_code=$?
            echo "Command failed with exit code $exit_code" >&2
            (( attempt++ ))
            
            if (( attempt <= max_attempts )); then
                sleep $((attempt * 2))  # Exponential backoff
            fi
        fi
    done
    
    echo "Failed after $max_attempts attempts" >&2
    return 1
}

# Example usage
run_with_retry "curl -s https://example.com" 5
```

### Debug Toolkit

```bash
#!/usr/bin/env bash

# Debug helper functions
debug() {
    if [[ "$DEBUG" == "1" ]]; then
        local frame=0
        local line_no func file
        while read -r line_no func file < <(caller $frame); do
            echo -e "\e[33m[DEBUG]\e[0m ${file##*/}:${func}:${line_no}: $*" >&2
            (( frame++ ))
            
            # Only show the first 2 frames of the call stack
            if (( frame >= 2 )); then
                break
            fi
        done
    fi
}

trace() {
    if [[ "$TRACE" == "1" ]]; then
        echo -e "\e[35m[TRACE]\e[0m \$@: $@" >&2
        
        # Print all parameters
        local i=1
        while (( i <= $# )); do
            echo -e "\e[35m[TRACE]\e[0m \$$i: ${!i}" >&2
            (( i++ ))
        done
    fi
}

# Enable debug/trace with environment variables
# DEBUG=1 ./script.sh
# TRACE=1 ./script.sh

# Toggle debug mode during execution
debug_on() {
    set -x
    DEBUG=1
    TRACE=1
}

debug_off() {
    set +x
    DEBUG=0
    TRACE=0
}

# Wrapper to debug a specific function
debug_function() {
    local func_name=$1
    shift
    
    debug_on
    "$func_name" "$@"
    local result=$?
    debug_off
    
    return $result
}

# Function to inspect variables
dump_vars() {
    for var in "$@"; do
        if [[ -n "${!var+x}" ]]; then
            echo "$var = ${!var}"
        else
            echo "$var is unset"
        fi
    done
}

# Debug example usage
example_function() {
    trace "Arguments received" "$@"
    local result="Calculated value"
    debug "result: $result"
    echo "$result"
}
```

### Interactive Debugging

```bash
#!/usr/bin/env bash

# Function for interactive debugging
debug_shell() {
    local stack_file=$(mktemp)
    trap 'rm -f "$stack_file"' EXIT
    
    # Capture the call stack
    local frame=0
    local line_no func file
    while read -r line_no func file < <(caller $frame); do
        echo "[$frame] $file:$line_no in function '$func'" >> "$stack_file"
        (( frame++ ))
    done
    
    # Show call stack
    cat "$stack_file"
    
    # Start an interactive shell
    local ps1_old=$PS1
    PS1="DEBUG[\$?]> "
    set +eu
    bash --norc
    set -eu
    PS1=$ps1_old
}

# Conditional breakpoint
breakpoint() {
    local condition=$1
    local msg=${2:-"Breakpoint hit"}
    
    if [[ -z "$condition" || "$condition" == "1" ]]; then
        echo -e "\e[31m$msg\e[0m"
        debug_shell
    fi
}

# Example usage
example() {
    local x=$1
    
    # Break only when x is greater than 10
    breakpoint "(( x > 10 ))" "Breaking because x = $x > 10"
    
    echo "x = $x"
}

# Call with the example
example 15
```

## Security Best Practices

### Input Sanitization

```bash
#!/usr/bin/env bash

# Limit length and sanitize input
sanitize_input() {
    local input=$1
    local max_length=${2:-100}
    
    # Truncate to maximum length
    input="${input:0:$max_length}"
    
    # Remove potentially dangerous characters
    input="${input//[;&|<>$\`\"\']/}"
    
    echo "$input"
}

# Validate numeric input
validate_integer() {
    local input=$1
    local min=${2:-0}
    local max=${3:-2147483647}
    
    if [[ ! $input =~ ^[0-9]+$ ]]; then
        echo "Error: Input must be a positive integer" >&2
        return 1
    fi
    
    if (( input < min || input > max )); then
        echo "Error: Input must be between $min and $max" >&2
        return 1
    fi
    
    return 0
}

# Validate and sanitize filename
sanitize_filename() {
    local filename=$1
    
    # Remove path components
    filename="${filename##*/}"
    
    # Remove special characters
    filename="${filename//[^a-zA-Z0-9._-]/}"
    
    # Ensure there's still something left
    if [[ -z "$filename" ]]; then
        filename="file"
    fi
    
    echo "$filename"
}

# Secure temporary file creation
secure_tempfile() {
    local prefix=${1:-"tmp"}
    local tempfile
    
    # Create file with restrictive permissions
    if tempfile=$(mktemp -p /tmp "${prefix}.XXXXXXXXXX" 2>/dev/null); then
        chmod 600 "$tempfile"
        echo "$tempfile"
        return 0
    else
        echo "Error: Failed to create temporary file" >&2
        return 1
    fi
}
```

### Safe Command Execution

```bash
#!/usr/bin/env bash

# Safer command execution with user input
safe_exec() {
    local cmd=$1
    shift
    
    # Validate command against an allowed list
    local allowed_cmds=("ls" "grep" "sed" "awk" "find")
    local allowed=0
    
    for allowed_cmd in "${allowed_cmds[@]}"; do
        if [[ "$cmd" == "$allowed_cmd" ]]; then
            allowed=1
            break
        fi
    done
    
    if (( allowed == 0 )); then
        echo "Error: Command '$cmd' not allowed" >&2
        return 1
    fi
    
    # Execute command with arguments passed properly
    "$cmd" "$@"
}

# Alternative approach using case
safe_command() {
    local cmd=$1
    shift
    
    case "$cmd" in
        ls)
            ls "$@"
            ;;
        grep)
            grep "$@"
            ;;
        *)
            echo "Error: Command '$cmd' not supported" >&2
            return 1
            ;;
    esac
}

# Safe file operations
safe_delete() {
    local path=$1
    
    # Validate path
    if [[ -z "$path" || "$path" == "/" || "$path" == "/home" ]]; then
        echo "Error: Invalid path '$path'" >&2
        return 1
    fi
    
    # Check path exists
    if [[ ! -e "$path" ]]; then
        echo "Error: Path '$path' does not exist" >&2
        return 1
    fi
    
    # Use -i for interactive mode and --preserve-root for safety
    rm -i --preserve-root "$path"
}

# Safe script execution
safe_run_script() {
    local script_path=$1
    shift
    
    # Check file exists and is owned by the user
    if [[ ! -f "$script_path" ]]; then
        echo "Error: Script not found: $script_path" >&2
        return 1
    fi
    
    if [[ "$(stat -c '%U' "$script_path")" != "$USER" ]]; then
        echo "Error: Script not owned by current user" >&2
        return 1
    fi
    
    # Check for insecure constructs in script
    if grep -q "eval.*\$" "$script_path"; then
        echo "Warning: Script contains potentially unsafe eval statements" >&2
    fi
    
    # Execute script with arguments
    "$script_path" "$@"
}
```

### Credential Handling

```bash
#!/usr/bin/env bash

# Function to prompt for password without echo
read_password() {
    local prompt=${1:-"Enter password: "}
    local password
    
    # Disable echo
    stty -echo
    read -p "$prompt" password
    stty echo
    echo  # Add newline after input
    
    echo "$password"
}

# Function to generate random password
generate_password() {
    local length=${1:-16}
    local chars="A-Za-z0-9!@#$%^&*()-_=+[]{}|;:,.<>?"
    
    # Generate random password
    local password=$(tr -dc "$chars" < /dev/urandom | head -c "$length")
    
    echo "$password"
}

# Securely store and retrieve credentials
store_credential() {
    local key=$1
    local value=$2
    local cred_file="$HOME/.app_credentials"
    
    # Create or update encrypted credential file
    if [[ ! -f "$cred_file" ]]; then
        touch "$cred_file"
        chmod 600 "$cred_file"
    fi
    
    # Use GPG if available, otherwise base64 encode (less secure)
    if command -v gpg >/dev/null 2>&1; then
        # Backup old file
        cp "$cred_file" "$cred_file.bak"
        
        # Create new file with updated credentials
        (
            grep -v "^$key=" "$cred_file" 2>/dev/null
            echo "$key=$(echo -n "$value" | gpg --symmetric --armor)"
        ) > "$cred_file.new"
        
        mv "$cred_file.new" "$cred_file"
        chmod 600 "$cred_file"
    else
        # Less secure fallback
        sed -i "/^$key=/d" "$cred_file" 2>/dev/null
        echo "$key=$(echo -n "$value" | base64)" >> "$cred_file"
    fi
}

get_credential() {
    local key=$1
    local cred_file="$HOME/.app_credentials"
    
    if [[ ! -f "$cred_file" ]]; then
        return 1
    fi
    
    local encoded_value=$(grep "^$key=" "$cred_file" | cut -d= -f2-)
    
    if [[ -z "$encoded_value" ]]; then
        return 1
    fi
    
    # Decode based on format
    if [[ "$encoded_value" == "-----BEGIN PGP MESSAGE-----"* ]]; then
        echo "$encoded_value" | gpg --decrypt
    else
        echo "$encoded_value" | base64 --decode
    fi
}
```

## Performance Optimization

### Reducing Process Overhead

```bash
#!/usr/bin/env bash

# Measure performance
time_execution() {
    local start_time=$(date +%s.%N)
    "$@"
    local end_time=$(date +%s.%N)
    
    local elapsed=$(echo "$end_time - $start_time" | bc)
    echo "Execution time: $elapsed seconds" >&2
}

# Avoid spawning processes in loops
process_file_inefficient() {
    while read -r line; do
        # Bad: Spawns a new process for each line
        echo "$line" | grep "pattern" | cut -d: -f2
    done < "$1"
}

process_file_efficient() {
    # Good: Single process pipeline
    grep "pattern" "$1" | cut -d: -f2
}

# Cache expensive operations
get_data_with_cache() {
    local cache_file="/tmp/data_cache"
    local cache_ttl=300  # 5 minutes
    
    if [[ -f "$cache_file" ]]; then
        local file_age=$(($(date +%s) - $(stat -c %Y "$cache_file")))
        if (( file_age < cache_ttl )); then
            # Use cached data
            cat "$cache_file"
            return 0
        fi
    fi
    
    # Cache miss - fetch new data
    fetch_expensive_data > "$cache_file"
    cat "$cache_file"
}

# Batch processing instead of per-item
process_files_batch() {
    # Process all files in a single command
    find . -name "*.log" -exec grep -l "ERROR" {} \; | xargs wc -l
}

# Using built-in string operations
parse_config_file() {
    local config_file=$1
    declare -A config
    
    while IFS="=" read -r key value; do
        # Skip comments and empty lines
        [[ "$key" =~ ^[[:space:]]*# || -z "$key" ]] && continue
        
        # Trim whitespace using parameter expansion (no subprocesses)
        key="${key#"${key%%[![:space:]]*}"}"
        key="${key%"${key##*[![:space:]]}"}"
        
        value="${value#"${value%%[![:space:]]*}"}"
        value="${value%"${value##*[![:space:]]}"}"
        
        config["$key"]="$value"
    done < "$config_file"
    
    # Return the array
    declare -p config
}
```

### Memory Optimization

```bash
#!/usr/bin/env bash

# Process large files line by line
process_large_file() {
    local file=$1
    
    # Use while-read pattern to avoid loading entire file
    while IFS= read -r line; do
        process_line "$line"
    done < "$file"
}

# Chunked processing for very large files
process_in_chunks() {
    local file=$1
    local chunk_size=${2:-10000}
    local total_lines=$(wc -l < "$file")
    
    for ((start=1; start<=total_lines; start+=chunk_size)); do
        local end=$((start + chunk_size - 1))
        if (( end > total_lines )); then
            end=$total_lines
        fi
        
        echo "Processing lines $start to $end..."
        sed -n "${start},${end}p" "$file" | process_chunk
    done
}

# Using named pipes for streaming processing
process_with_pipe() {
    local file=$1
    local pipe=$(mktemp -u)
    
    mkfifo "$pipe"
    trap 'rm -f "$pipe"' EXIT
    
    # Producer - read file in background
    {
        cat "$file"
        echo "EOF"
    } > "$pipe" &
    
    # Consumer - process data as it becomes available
    local line
    while read -r line; do
        if [[ "$line" == "EOF" ]]; then
            break
        fi
        process_line "$line"
    done < "$pipe"
}

# Reducing memory usage in arrays
process_large_dataset() {
    local file=$1
    local window_size=1000
    local lines=()
    local count=0
    
    while IFS= read -r line; do
        lines+=("$line")
        ((count++))
        
        # Process in windows to limit memory usage
        if (( count >= window_size )); then
            process_lines "${lines[@]}"
            lines=()
            count=0
        fi
    done < "$file"
    
    # Process remaining lines
    if (( count > 0 )); then
        process_lines "${lines[@]}"
    fi
}
```

### CPU Optimization

```bash
#!/usr/bin/env bash

# Multi-threaded processing with xargs
parallel_process() {
    local cores=$(nproc)
    find . -name "*.txt" | xargs -P "$cores" -I{} grep -l "pattern" {}
}

# Split work by CPU count
process_by_cpu() {
    local cores=$(nproc)
    local file=$1
    local total_lines=$(wc -l < "$file")
    local lines_per_core=$((total_lines / cores + 1))
    
    for ((i=0; i<cores; i++)); do
        local start=$((i * lines_per_core + 1))
        local end=$((start + lines_per_core - 1))
        
        if (( end > total_lines )); then
            end=$total_lines
        fi
        
        # Process this chunk in background
        {
            echo "Worker $i processing lines $start-$end"
            sed -n "${start},${end}p" "$file" | process_chunk "$i"
        } &
    done
    
    wait  # Wait for all background jobs
}

# Optimize string operations
optimize_string_concat() {
    local iterations=10000
    local result=""
    
    # Inefficient: String concatenation in loop
    for ((i=0; i<iterations; i++)); do
        result="$result$i"  # Creates a new string each time
    done
    
    # More efficient: Use an array and join at the end
    local parts=()
    for ((i=0; i<iterations; i++)); do
        parts+=("$i")
    done
    local efficient_result=$(IFS= ; echo "${parts[*]}")
    
    echo "Inefficient length: ${#result}"
    echo "Efficient length: ${#efficient_result}"
}

# Avoid expensive regex when simple string ops will do
optimize_string_check() {
    local input=$1
    
    # Inefficient: Using regex for simple prefix check
    if [[ "$input" =~ ^prefix ]]; then
        echo "Has prefix (regex)"
    fi
    
    # More efficient: Using parameter expansion
    if [[ "$input" == prefix* ]]; then
        echo "Has prefix (parameter expansion)"
    fi
}
```

## Advanced Text Processing

### Advanced Sed Techniques

```bash
#!/usr/bin/env bash

# Multi-line pattern matching with sed
sed_multiline() {
    # Match content between START and END patterns
    sed -n '/START/,/END/p' file.txt
    
    # Delete content between patterns
    sed '/START/,/END/d' file.txt
    
    # Replace content between patterns
    sed '/START/,/END/c\
New content replacing everything between START and END' file.txt
    
    # Insert after a pattern
    sed '/pattern/a\
Line 1\
Line 2\
Line 3' file.txt
    
    # Execute multiple commands on matching lines
    sed -e '/pattern/{s/old/new/g; s/foo/bar/g; p; d}' file.txt
    
    # Conditional replacement
    sed '/^#/!s/FOO/BAR/g' file.txt  # Replace only in non-comment lines
}

# Sed with capture groups
sed_capture() {
    # Reordering captured groups
    sed -r 's/([A-Z]+) ([0-9]+)/\2-\1/g' file.txt
    
    # Extract specific data
    sed -rn 's/.*User: ([A-Za-z0-9]+).*/\1/p' log.txt
    
    # Multiple captures
    sed -r 's/(www\.)([^.]+)(\.com)/\2/g' urls.txt  # Extract domain name
    
    # Nested capture groups
    sed -r 's/((A|B)(C|D))/\2\3-\1/g' file.txt
}

# Advanced substitution
sed_advanced_subst() {
    # Substituting newlines
    sed ':a;N;$!ba;s/\n/ /g' file.txt  # Replace all newlines with spaces
    
    # Using & to refer to matched pattern
    sed 's/[0-9]\+/\*\*&\*\*/g' file.txt  # Wrap numbers in asterisks
    
    # Conditional logic with branching
    sed -e '/pattern/{s/old/new/; b}; s/other/replacement/' file.txt
    
    # Case conversion
    sed 's/\(.*\)/\L\1/' file.txt  # Convert to lowercase
    sed 's/\(.*\)/\U\1/' file.txt  # Convert to uppercase
    sed 's/\<\(.\)\(.*\)/\u\1\2/g' file.txt  # Capitalize first letter of each word
}
```

### Advanced Awk Programming

```bash
#!/usr/bin/env bash

# Using arrays in awk
awk_arrays() {
    # Count word frequency
    awk '
    {
        for (i=1; i<=NF; i++) {
            word = tolower($i)
            # Remove punctuation
            gsub(/[^a-z]/, "", word)
            if (word != "") count[word]++
        }
    }
    END {
        print "Word frequencies:"
        for (word in count)
            printf "%-15s %5d\n", word, count[word]
    }' file.txt
    
    # Two-dimensional arrays
    awk '
    {
        day = $1
        hour = $2
        count[day,hour]++
    }
    END {
        print "Activity by day and hour:"
        for (dayhour in count) {
            split(dayhour, parts, SUBSEP)
            printf "Day: %s, Hour: %s, Count: %d\n", 
                   parts[1], parts[2], count[dayhour]
        }
    }' access.log
}

# Advanced math with awk
awk_math() {
    # Calculate statistics
    awk '
    {
        sum += $1
        sumsq += $1 * $1
        count++
        if (min == "" || $1 < min) min = $1
        if (max == "" || $1 > max) max = $1
        data[count] = $1
    }
    END {
        # Sort the array for median
        asort(data)
        
        # Calculate median
        if (count % 2) {
            median = data[int(count/2) + 1]
        } else {
            median = (data[count/2] + data[count/2 + 1]) / 2
        }
        
        # Calculate mean and standard deviation
        mean = sum / count
        stddev = sqrt(sumsq/count - mean*mean)
        
        printf "Count: %d\n", count
        printf "Sum: %.2f\n", sum
        printf "Min: %.2f\n", min
        printf "Max: %.2f\n", max
        printf "Mean: %.2f\n", mean
        printf "Median: %.2f\n", median
        printf "StdDev: %.2f\n", stddev
    }' numeric_data.txt
    
    # Linear regression
    awk '
    {
        x[NR] = $1
        y[NR] = $2
        sumx += $1
        sumy += $2
        sumxy += $1 * $2
        sumxx += $1 * $1
    }
    END {
        n = NR
        meanx = sumx / n
        meany = sumy / n
        
        # Calculate slope and intercept
        slope = (sumxy - sumx * sumy / n) / (sumxx - sumx * sumx / n)
        intercept = meany - slope * meanx
        
        printf "Linear regression: y = %.4fx + %.4f\n", slope, intercept
        
        # Calculate R-squared
        for (i=1; i<=n; i++) {
            fitted = intercept + slope * x[i]
            sse += (y[i] - fitted)^2
            sst += (y[i] - meany)^2
        }
        rsquared = 1 - sse/sst
        printf "R-squared: %.4f\n", rsquared
    }' xy_data.txt
}

# Text report generation with awk
awk_report() {
    # Generate HTML report from CSV
    awk -F, '
    BEGIN {
        print "<html><head><title>Report</title></head><body>"
        print "<table border=\"1\">"
        print "<tr><th>Name</th><th>Department</th><th>Salary</th></tr>"
    }
    NR > 1 { # Skip header row
        print "<tr>"
        print "<td>" $1 "</td>"
        print "<td>" $2 "</td>"
        print "<td>$" $3 "</td>"
        print "</tr>"
        
        # Collect statistics by department
        dept_count[$2]++
        dept_salary[$2] += $3
    }
    END {
        print "</table>"
        
        print "<h2>Department Summary</h2>"
        print "<table border=\"1\">"
        print "<tr><th>Department</th><th>Employees</th><th>Total Salary</th><th>Average Salary</th></tr>"
        
        for (dept in dept_count) {
            avg = dept_salary[dept] / dept_count[dept]
            print "<tr>"
            print "<td>" dept "</td>"
            print "<td>" dept_count[dept] "</td>"
            print "<td>$" dept_salary[dept] "</td>"
            print "<td>$" avg "</td>"
            print "</tr>"
        }
        
        print "</table>"
        print "</body></html>"
    }' employees.csv > report.html
}
```

### Jq for JSON Processing

```bash
#!/usr/bin/env bash

# Basic JSON parsing with jq
jq_basics() {
    local json_file=$1
    
    # Extract specific fields
    jq '.name' "$json_file"
    
    # Extract nested fields
    jq '.address.city' "$json_file"
    
    # Extract array elements
    jq '.items[0]' "$json_file"
    jq '.items[-1]' "$json_file"  # Last element
    
    # Iterate through array
    jq '.items[]' "$json_file"
    
    # Format output as plain text
    jq -r '.name' "$json_file"
    
    # Multiple selections
    jq '{name: .name, city: .address.city}' "$json_file"
}

# Advanced JSON transformation
jq_transform() {
    local json_file=$1
    
    # Filter array elements
    jq '.items[] | select(.price > 10)' "$json_file"
    
    # Transform array structure
    jq '.items[] | {item_name: .name, cost: .price}' "$json_file"
    
    # Aggregate data
    jq '[.items[].price] | add' "$json_file"  # Sum prices
    jq '[.items[].price] | length, min, max, add/length' "$json_file"  # Stats
    
    # Group by and count
    jq 'group_by(.category) | map({category: .[0].category, count: length})' "$json_file"
    
    # Sort results
    jq '.items | sort_by(.price)' "$json_file"
    jq '.items | sort_by(.price) | reverse' "$json_file"  # Descending
    
    # Pipe to bash variables
    local item_count=$(jq '.items | length' "$json_file")
    echo "Item count: $item_count"
    
    # Create new JSON
    jq -n --arg name "John" --arg age "30" '{name: $name, age: ($age|tonumber)}' > person.json
}

# Real-world jq examples
jq_examples() {
    # Process API response
    curl -s 'https://api.example.com/users' | jq '.[]| select(.active == true) | .email'
    
    # Convert between formats
    jq -r '.[] | [.id, .name, .email] | @csv' users.json > users.csv
    
    # Extract values for use in bash script
    user_ids=$(jq -r '.[] | .id' users.json)
    for id in $user_ids; do
        echo "Processing user $id..."
    done
    
    # Complex data transformation
    jq '
        .items | 
        map({
            id: .id,
            name: .name,
            value: (.price * .quantity),
            in_stock: (.quantity > 0)
        }) |
        sort_by(.value) |
        reverse |
        {
            total_items: length,
            total_value: map(.value) | add,
            items: .
        }' inventory.json > report.json
}
```

## Bash Networking

### HTTP Requests

```bash
#!/usr/bin/env bash

# GET request with curl
curl_get() {
    local url=$1
    local outfile=${2:-}
    
    if [[ -n "$outfile" ]]; then
        curl -s -o "$outfile" "$url"
    else
        curl -s "$url"
    fi
}

# POST request with curl
curl_post() {
    local url=$1
    local data=$2
    local content_type=${3:-"application/json"}
    
    curl -s -X POST \
        -H "Content-Type: $content_type" \
        -d "$data" \
        "$url"
}

# File upload with curl
curl_upload() {
    local url=$1
    local file=$2
    local field_name=${3:-"file"}
    
    curl -s -X POST \
        -F "$field_name=@$file" \
        "$url"
}

# API request with authentication
curl_auth_request() {
    local url=$1
    local method=${2:-"GET"}
    local token=$3
    local data=${4:-""}
    
    curl -s -X "$method" \
        -H "Authorization: Bearer $token" \
        -H "Content-Type: application/json" \
        ${data:+-d "$data"} \
        "$url"
}

# Download with progress and retry
download_with_retry() {
    local url=$1
    local output_file=$2
    local max_retries=${3:-3}
    local retry_count=0
    
    while (( retry_count < max_retries )); do
        if curl -L -o "$output_file" \
            --retry 3 \
            --retry-delay 5 \
            --retry-max-time 30 \
            --connect-timeout 30 \
            --progress-bar \
            "$url"; then
            echo "Download complete: $output_file"
            return 0
        else
            (( retry_count++ ))
            echo "Download failed. Retry $retry_count of $max_retries..."
            sleep $((retry_count * 5))  # Exponential backoff
        fi
    done
    
    echo "Failed to download after $max_retries attempts" >&2
    return 1
}

# API pagination handler
fetch_paginated_api() {
    local base_url=$1
    local per_page=${2:-100}
    local max_pages=${3:-10}
    local output_file=${4:-"results.json"}
    
    local page=1
    local has_more=true
    local tmp_file=$(mktemp)
    trap 'rm -f "$tmp_file"' EXIT
    
    # Initialize results array
    echo "[]" > "$output_file"
    
    while [[ "$has_more" == "true" && $page -le $max_pages ]]; do
        echo "Fetching page $page..."
        
        local response=$(curl -s "${base_url}?page=${page}&per_page=${per_page}")
        local items_count=$(echo "$response" | jq '. | length')
        
        if (( items_count == 0 )); then
            has_more=false
        else
            # Merge with existing results
            jq -s '.[0] + .[1]' "$output_file" <(echo "$response") > "$tmp_file"
            mv "$tmp_file" "$output_file"
            
            (( page++ ))
        fi
    done
    
    local total_items=$(jq '. | length' "$output_file")
    echo "Fetched $total_items items across $((page - 1)) pages"
}
```

### Socket Programming

```bash
#!/usr/bin/env bash

# Simple TCP client with /dev/tcp
tcp_client() {
    local host=$1
    local port=$2
    local message=$3
    
    # Check if /dev/tcp is available (Bash feature)
    if [[ ! -w /dev/tcp ]]; then
        echo "Error: /dev/tcp is not available" >&2
        return 1
    fi
    
    # Connect and send message
    exec 3<>/dev/tcp/"$host"/"$port"
    
    echo -e "$message" >&3
    
    # Read response with timeout
    timeout 5 cat <&3
    
    # Close connection
    exec 3>&-
}

# HTTP client using /dev/tcp
http_get() {
    local host=$1
    local path=${2:-"/"}
    local port=${3:-80}
    
    exec 3<>/dev/tcp/"$host"/"$port"
    
    # Send HTTP GET request
    cat <<EOF >&3
GET $path HTTP/1.1
Host: $host
User-Agent: bash/$BASH_VERSION
Connection: close

EOF
    
    # Read response
    cat <&3
    
    # Close connection
    exec 3>&-
}

# Simple port scanner
port_scan() {
    local host=$1
    local start_port=${2:-1}
    local end_port=${3:-1024}
    local timeout=${4:-1}
    
    echo "Scanning $host from port $start_port to $end_port..."
    
    for ((port=start_port; port<=end_port; port++)); do
        (
            # Try to connect with timeout
            if timeout "$timeout" bash -c "echo > /dev/tcp/$host/$port" 2>/dev/null; then
                echo "Port $port: Open"
            fi
        ) &
        
        # Limit concurrent processes
        if (( (port - start_port) % 20 == 0 )); then
            wait
        fi
    done
    
    wait  # Wait for remaining background processes
}

# Check service availability
check_service() {
    local host=$1
    local port=$2
    local service=${3:-"Service"}
    local timeout=${4:-5}
    
    if timeout "$timeout" bash -c "echo > /dev/tcp/$host/$port" 2>/dev/null; then
        echo "$service is UP ($host:$port)"
        return 0
    else
        echo "$service is DOWN ($host:$port)"
        return 1
    fi
}

# Simple HTTP server using netcat (requires nc)
http_server() {
    local port=${1:-8080}
    local root_dir=${2:-"."}
    
    if ! command -v nc >/dev/null 2>&1; then
        echo "Error: netcat (nc) is required but not installed" >&2
        return 1
    fi
    
    echo "Starting HTTP server on port $port, serving $root_dir"
    
    while true; do
        nc -l -p "$port" -c "handle_request \"$root_dir\""
    done
}

handle_request() {
    local root_dir=$1
    
    # Read the first line of the HTTP request
    read -r request_line
    
    # Extract the HTTP method and path
    local method=$(echo "$request_line" | cut -d ' ' -f1)
    local path=$(echo "$request_line" | cut -d ' ' -f2)
    
    # Remove leading slash and add root directory
    local file_path="${root_dir}/${path#/}"
    
    # Default to index.html for root path
    if [[ "$file_path" == "$root_dir/" ]]; then
        file_path="${root_dir}/index.html"
    fi
    
    # Consume the rest of the HTTP headers
    while read -r line; do
        [[ -z "$line" || "$line" == $'\r' ]] && break
    done
    
    # Check if the file exists
    if [[ -f "$file_path" ]]; then
        echo -e "HTTP/1.1 200 OK\r"
        echo -e "Content-Type: $(get_content_type "$file_path")\r"
        echo -e "\r"
        cat "$file_path"
    else
        echo -e "HTTP/1.1 404 Not Found\r"
        echo -e "Content-Type: text/plain\r"
        echo -e "\r"
        echo "404 Not Found: $path"
    fi
}

get_content_type() {
    local file=$1
    local ext="${file##*.}"
    
    case "$ext" in
        html|htm) echo "text/html" ;;
        js)       echo "application/javascript" ;;
        css)      echo "text/css" ;;
        jpg|jpeg) echo "image/jpeg" ;;
        png)      echo "image/png" ;;
        gif)      echo "image/gif" ;;
        *)        echo "text/plain" ;;
    esac
}
```

### Secure Shell Operations

```bash
#!/usr/bin/env bash

# Execute command on remote server
remote_execute() {
    local host=$1
    local user=${2:-$(whoami)}
    local cmd=$3
    local key=${4:-"$HOME/.ssh/id_rsa"}
    
    ssh -i "$key" -o StrictHostKeyChecking=no "$user@$host" "$cmd"
}

# Secure copy file to remote server
remote_copy() {
    local src=$1
    local host=$2
    local dest=$3
    local user=${4:-$(whoami)}
    local key=${5:-"$HOME/.ssh/id_rsa"}
    
    scp -i "$key" -o StrictHostKeyChecking=no "$src" "$user@$host:$dest"
}

# Rsync with SSH
remote_sync() {
    local src=$1
    local host=$2
    local dest=$3
    local user=${4:-$(whoami)}
    local key=${5:-"$HOME/.ssh/id_rsa"}
    local options=${6:-"-avz --delete"}
    
    rsync "$options" -e "ssh -i $key -o StrictHostKeyChecking=no" \
        "$src" "$user@$host:$dest"
}

# Monitor remote log file
remote_tail() {
    local host=$1
    local log_file=$2
    local user=${3:-$(whoami)}
    local key=${4:-"$HOME/.ssh/id_rsa"}
    local lines=${5:-10}
    
    ssh -i "$key" -o StrictHostKeyChecking=no "$user@$host" \
        "tail -f -n $lines $log_file"
}

# Execute command on multiple servers
batch_execute() {
    local hosts_file=$1
    local cmd=$2
    local user=${3:-$(whoami)}
    local key=${4:-"$HOME/.ssh/id_rsa"}
    local timeout=${5:-30}
    
    if [[ ! -f "$hosts_file" ]]; then
        echo "Error: Hosts file not found: $hosts_file" >&2
        return 1
    fi
    
    while read -r host; do
        # Skip empty lines and comments
        [[ -z "$host" || "$host" == \#* ]] && continue
        
        echo "=== Executing on $host ==="
        timeout "$timeout" ssh -i "$key" -o StrictHostKeyChecking=no \
            -o ConnectTimeout=5 "$user@$host" "$cmd"
        echo "=== Completed on $host ==="
        echo
    done < "$hosts_file"
}

# SSH tunnel
create_tunnel() {
    local local_port=$1
    local remote_host=$2
    local remote_port=$3
    local gateway=$4
    local user=${5:-$(whoami)}
    local key=${6:-"$HOME/.ssh/id_rsa"}
    
    # Create SSH tunnel
    ssh -i "$key" -f -N -L "$local_port:$remote_host:$remote_port" "$user@$gateway"
    
    echo "Tunnel created: localhost:$local_port -> $remote_host:$remote_port via $user@$gateway"
    echo "To stop the tunnel: pkill -f '$remote_host:$remote_port'"
}
```

## File Descriptors and Redirection

### Advanced Redirection Techniques

```bash
#!/usr/bin/env bash

# Save all output to a file
{
    echo "This goes to the file"
    echo "This also goes to the file"
    echo "Error message" >&2  # Still goes to the file
} > output.log 2>&1

# Save stdout and stderr to different files
{
    echo "Standard output"
    echo "Error message" >&2
} 1> stdout.log 2> stderr.log

# Log both to file and display on screen
exec 3>&1  # Save original stdout
{
    echo "This message goes to both the screen and the log file"
    echo "This error message too" >&2
} 2>&1 | tee -a output.log >&3
exec 3>&-  # Close FD 3

# Redirect specific file descriptors
exec 4>custom.log  # Open FD 4 for writing
echo "This goes to custom.log" >&4
echo "This goes to stdout"
exec 4>&-  # Close FD 4

# Redirect both stdout and stderr to different commands
{
    echo "Normal output"
    echo "Error output" >&2
} > >(grep "Normal" > stdout.log) 2> >(grep "Error" > stderr.log)

# Silence a command except for specific error patterns
{
    verbose_command
} > /dev/null 2> >(grep -i "error\|warning" >&2)

# Implement a simple logging system
log() {
    local level=$1
    shift
    local message="$*"
    local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    
    case "$level" in
        INFO)    echo "$timestamp [INFO] $message" >> info.log ;;
        WARNING) echo "$timestamp [WARNING] $message" >> warn.log ;;
        ERROR)   echo "$timestamp [ERROR] $message" | tee -a error.log >&2 ;;
        DEBUG)   [[ "$DEBUG" == "1" ]] && echo "$timestamp [DEBUG] $message" >> debug.log ;;
        *)       echo "$timestamp [LOG] $message" >> app.log ;;
    esac
}
```

### File Descriptor Manipulation

```bash
#!/usr/bin/env bash

# Reading from and writing to the same file
read_write_same_file() {
    local file=$1
    
    # Wrong approach (truncates the file before reading):
    # sort "$file" > "$file"
    
    # Correct approach using file descriptors:
    exec 3<> "$file"    # Open for reading and writing
    exec 4> "$file.tmp" # Temporary output file
    
    sort <&3 >&4        # Read from FD 3, write to FD 4
    
    exec 3>&- 4>&-      # Close file descriptors
    
    mv "$file.tmp" "$file"
}

# Create a read/write communication channel
create_channel() {
    local pipe_in=$(mktemp -u)
    local pipe_out=$(mktemp -u)
    
    mkfifo "$pipe_in" "$pipe_out"
    
    # Open persistent file descriptors
    exec 8<> "$pipe_in"
    exec 9<> "$pipe_out"
    
    # Clean up named pipes
    rm "$pipe_in" "$pipe_out"
    
    echo "Channel created (FD 8 for reading, FD 9 for writing)"
}

# Write to the channel
write_channel() {
    local message=$1
    echo "$message" >&9
}

# Read from the channel (non-blocking)
read_channel() {
    local timeout=${1:-0}
    
    if (( timeout > 0 )); then
        # With timeout
        read -t "$timeout" -u 8 line && echo "$line" || echo "Timeout"
    else
        # Non-blocking read
        if read -t 0 -u 8 line; then
            echo "$line"
        else
            echo "No data available"
        fi
    fi
}

# Close the channel
close_channel() {
    exec 8>&- 9>&-
    echo "Channel closed"
}

# Implement a bidirectional pipe
bidirectional_pipe() {
    local cmd1=$1
    local cmd2=$2
    
    # Create pipes
    local pipe1=$(mktemp -u)
    local pipe2=$(mktemp -u)
    
    mkfifo "$pipe1" "$pipe2"
    
    # Run commands with crossed pipes
    "$cmd1" < "$pipe1" > "$pipe2" &
    pid1=$!
    
    "$cmd2" < "$pipe2" > "$pipe1" &
    pid2=$!
    
    # Cleanup on exit
    trap 'kill $pid1 $pid2 2>/dev/null; rm -f "$pipe1" "$pipe2"' EXIT
    
    # Wait for either process to finish
    wait -n
}
```

### Stream Manipulation

```bash
#!/usr/bin/env bash

# Tee with multiple outputs
multi_tee() {
    local input=${1:-/dev/stdin}
    local outputs=("${@:2}")
    
    if [[ ${#outputs[@]} -eq 0 ]]; then
        echo "Error: No output files specified" >&2
        return 1
    fi
    
    # Create a command that writes to all outputs
    local cmd="tee"
    for ((i=0; i<${#outputs[@]}-1; i++)); do
        cmd="$cmd ${outputs[i]} |"
    done
    cmd="$cmd ${outputs[-1]}"
    
    # Run the command with input
    cat "$input" | eval "$cmd"
}

# Split a stream by pattern
split_stream() {
    local input=${1:-/dev/stdin}
    local pattern=$2
    local true_output=${3:-matched.txt}
    local false_output=${4:-unmatched.txt}
    
    # Process input line by line
    while IFS= read -r line; do
        if [[ "$line" =~ $pattern ]]; then
            echo "$line" >> "$true_output"
        else
            echo "$line" >> "$false_output"
        fi
    done < "$input"
}

# Process a stream in chunks
process_stream_chunks() {
    local input=${1:-/dev/stdin}
    local chunk_size=${2:-100}
    local processor=${3:-"cat"}
    
    # Process input in chunks
    chunk=()
    while IFS= read -r line; do
        chunk+=("$line")
        
        if (( ${#chunk[@]} >= chunk_size )); then
            # Process the chunk
            printf "%s\n" "${chunk[@]}" | eval "$processor"
            
            # Reset chunk
            chunk=()
        fi
    done < "$input"
    
    # Process the last chunk if not empty
    if (( ${#chunk[@]} > 0 )); then
        printf "%s\n" "${chunk[@]}" | eval "$processor"
    fi
}

# Create a logger that writes to multiple outputs
multi_logger() {
    local app_name=$1
    local log_file=${2:-/var/log/${app_name}.log}
    local error_file=${3:-/var/log/${app_name}_error.log}
    
    # Create or truncate log files
    > "$log_file"
    > "$error_file"
    
    # Set up file descriptors
    exec 3> >(
        while read -r line; do
            echo "[$(date "+%Y-%m-%d %H:%M:%S")] [INFO] $line" | tee -a "$log_file"
        done
    )
    
    exec 4> >(
        while read -r line; do
            echo "[$(date "+%Y-%m-%d %H:%M:%S")] [ERROR] $line" | tee -a "$log_file" | tee -a "$error_file" >&2
        done
    )
    
    # Define logging functions
    log_info() {
        echo "$*" >&3
    }
    
    log_error() {
        echo "$*" >&4
    }
    
    # Export functions
    export -f log_info log_error
    
    echo "Logger initialized. Use log_info and log_error functions."
}
```

## Subshells and Coprocesses

### Subshell Techniques

```bash
#!/usr/bin/env bash

# Preserve environment with subshells
preserve_environment() {
    local var="original"
    
    # Changes in a subshell don't affect the parent
    (
        var="changed"
        echo "Inside subshell: $var"  # Prints "changed"
    )
    
    echo "After subshell: $var"  # Prints "original"
    
    # Capture output but preserve environment
    var="original"
    output=$(var="capture this"; echo "$var")
    echo "output: $output"  # Prints "capture this"
    echo "var: $var"        # Prints "original"
}

# Subshell with local state
subshell_local_state() {
    # Create a subshell with its own environment
    (
        # Local variables
        local_var="subshell only"
        
        # Change directory without affecting parent
        cd /tmp
        
        # Set traps that only affect this subshell
        trap 'echo "Subshell trapped EXIT"' EXIT
        
        echo "Subshell working directory: $(pwd)"
        echo "Subshell local variable: $local_var"
    )
    
    echo "Parent shell working directory: $(pwd)"
    echo "Parent can't see subshell variable: ${local_var:-undefined}"
}

# Parallel execution with subshells
parallel_subshells() {
    local start_time=$(date +%s)
    
    # Run three commands in parallel
    (sleep 3; echo "Task 1 complete") &
    (sleep 2; echo "Task 2 complete") &
    (sleep 1; echo "Task 3 complete") &
    
    # Wait for all background jobs to complete
    wait
    
    local end_time=$(date +%s)
    echo "All tasks completed in $((end_time - start_time)) seconds"
}

# Nested subshells
nested_subshells() {
    var1="level 1"
    
    (
        var2="level 2"
        echo "Level 1 subshell: var1=$var1, var2=$var2"
        
        (
            var3="level 3"
            echo "Level 2 subshell: var1=$var1, var2=$var2, var3=$var3"
            
            (
                var1="modified in level 3"
                echo "Level 3 subshell: var1=$var1, var2=$var2, var3=$var3"
            )
            
            echo "Back to level 2: var1=$var1 (unchanged)"
        )
    )
    
    echo "Parent shell: var1=$var1 (unchanged), var2=${var2:-undefined}"
}

# Subshell for temporary environment changes
temporary_environment() {
    # Save the original environment variables
    local old_path=$PATH
    
    # Start a subshell with modified environment
    (
        # Modify PATH for this subshell only
        PATH="/custom/path:$PATH"
        
        # Run a command with the modified PATH
        echo "Subshell PATH: $PATH"
        command -v python
    )
    
    # Original environment is preserved
    echo "Original PATH: $PATH"
    
    # Verify PATH is unchanged
    [[ "$PATH" == "$old_path" ]] && echo "PATH unchanged"
}
```

### Coprocess Techniques

```bash
#!/usr/bin/env bash

# Basic coprocess
basic_coprocess() {
    # Start a coprocess
    coproc CALC { 
        while read -r line; do
            result=$(( line ))
            echo "$result"
        done
    }
    
    # Send data to coprocess
    echo "2 + 2" >&${CALC[1]}
    echo "10 * 5" >&${CALC[1]}
    echo "100 / 5" >&${CALC[1]}
    
    # Read results from coprocess
    read -r result <&${CALC[0]}
    echo "2 + 2 = $result"
    
    read -r result <&${CALC[0]}
    echo "10 * 5 = $result"
    
    read -r result <&${CALC[0]}
    echo "100 / 5 = $result"
    
    # Close coprocess
    exec {CALC[1]}>&-
    wait $CALC_PID
}

# Interactive coprocesses
interactive_coprocess() {
    # Start a Python interpreter as a coprocess
    coproc PYTHON { python3 -i; }
    
    # Give Python time to start up
    sleep 0.5
    
    # Define a function to send commands to Python
    run_python() {
        local cmd=$1
        echo "$cmd" >&${PYTHON[1]}
        
        # Read output from Python (this is a simplification)
        sleep 0.1
        read -r -t 1 output <&${PYTHON[0]}
        echo "Python output: $output"
    }
    
    # Run some Python commands
    run_python "print(2 + 2)"
    run_python "import math"
    run_python "print(math.sqrt(16))"
    
    # Cleanup
    echo "exit()" >&${PYTHON[1]}
    wait $PYTHON_PID
}

# Pipeline with coprocess
pipeline_coprocess() {
    # Create a coprocess for a complex pipeline
    coproc FILTER {
        grep "ERROR" |
        cut -d ' ' -f 3- |
        sort |
        uniq -c |
        sort -nr
    }
    
    # Send data to the pipeline
    cat <<EOF >&${FILTER[1]}
2023-01-01 12:00:00 ERROR Failed to connect to database
2023-01-01 12:05:00 INFO Connection established
2023-01-01 12:10:00 ERROR Failed to connect to database
2023-01-01 12:15:00 ERROR Invalid credentials
2023-01-01 12:20:00 WARN Slow response time
2023-01-01 12:25:00 ERROR Failed to connect to database
EOF
    
    # Close the input to signal EOF
    exec {FILTER[1]}>&-
    
    # Read all output
    while read -r line <&${FILTER[0]}; do
        echo "Filtered: $line"
    done
    
    # Wait for coprocess to finish
    wait $FILTER_PID
}

# Bidirectional communication with coprocess
bidirectional_coprocess() {
    # Start a coprocess for reverse text
    coproc REVERSE {
        while read -r text; do
            if [[ "$text" == "exit" ]]; then
                break
            fi
            echo "$text" | rev
        done
    }
    
    # Handle bidirectional communication
    for text in "Hello, World!" "Bash Scripting" "Coprocesses are useful"; do
        # Send text to coprocess
        echo "$text" >&${REVERSE[1]}
        
        # Read response from coprocess
        read -r response <&${REVERSE[0]}
        echo "Original: '$text', Reversed: '$response'"
    done
    
    # Terminate the coprocess
    echo "exit" >&${REVERSE[1]}
    wait $REVERSE_PID
}
```

## Bash Arrays and Associative Arrays

### Advanced Array Operations

```bash
#!/usr/bin/env bash

# Declare arrays
declare -a numeric_array    # Numeric indexed array
declare -A associative_array  # Associative array

# Initialize arrays
numeric_array=("apple" "banana" "cherry" "date")
associative_array=(
    [fruit]="apple"
    [vegetable]="carrot"
    [protein]="chicken"
    [grain]="rice"
)

# Array operations
array_operations() {
    local fruits=("apple" "banana" "cherry" "date" "elderberry")
    
    # Length of array
    echo "Array length: ${#fruits[@]}"
    
    # Length of element
    echo "Length of fruits[2]: ${#fruits[2]}"
    
    # Concatenate arrays
    local more_fruits=("fig" "grape")
    local all_fruits=("${fruits[@]}" "${more_fruits[@]}")
    echo "Combined array: ${all_fruits[*]}"
    
    # Slice array (offset length)
    echo "Slice [1:3]: ${fruits[@]:1:3}"
    
    # Slice from offset to end
    echo "Slice [2:]: ${fruits[@]:2}"
    
    # Replace elements
    local modified=("${fruits[@]/berry/fruit}")
    echo "After replacement: ${modified[*]}"
    
    # Delete element
    unset 'fruits[1]'
    echo "After deletion: ${fruits[*]}"
    
    # Re-index array (create a new array with consecutive indices)
    fruits=("${fruits[@]}")
    echo "Re-indexed: ${fruits[*]}"
    
    # Find index of element
    find_index() {
        local array=("$@")
        local value="${array[-1]}"
        unset 'array[-1]'
        
        for i in "${!array[@]}"; do
            if [[ "${array[$i]}" == "$value" ]]; then
                echo "$i"
                return 0
            fi
        done
        echo "-1"
        return 1
    }
    
    local idx=$(find_index "${fruits[@]}" "cherry")
    echo "Index of 'cherry': $idx"
}

# Array transformation functions
array_transformations() {
    local numbers=(10 20 30 40 50)
    
    # Map: Apply function to each element
    map() {
        local -n array=$1
        local func=$2
        local result=()
        
        for item in "${array[@]}"; do
            result+=("$(eval "$func $item")")
        done
        
        echo "${result[*]}"
    }
    
    # Example map: Double each number
    double() { echo $(($1 * 2)); }
    local doubled=$(map numbers double)
    echo "Doubled: $doubled"
    
    # Filter: Keep elements matching condition
    filter() {
        local -n array=$1
        local func=$2
        local result=()
        
        for item in "${array[@]}"; do
            if eval "$func $item"; then
                result+=("$item")
            fi
        done
        
        echo "${result[*]}"
    }
    
    # Example filter: Keep numbers greater than 25
    greater_than_25() { [[ $1 -gt 25 ]]; }
    local filtered=$(filter numbers greater_than_25)
    echo "Filtered: $filtered"
    
    # Reduce: Combine elements
    reduce() {
        local -n array=$1
        local func=$2
        local initial=$3
        local result=$initial
        
        for item in "${array[@]}"; do
            result=$(eval "$func $result $item")
        done
        
        echo "$result"
    }
    
    # Example reduce: Sum all numbers
    add() { echo $(($1 + $2)); }
    local sum=$(reduce numbers add 0)
    echo "Sum: $sum"
}

# Sorting arrays
array_sorting() {
    local fruits=("banana" "apple" "cherry" "date" "apricot")
    local numbers=(42 15 8 23 4 16)
    
    # Sort strings (simple)
    local sorted_fruits=($(printf '%s\n' "${fruits[@]}" | sort))
    echo "Sorted fruits: ${sorted_fruits[*]}"
    
    # Sort strings (case-insensitive)
    local case_sorted=($(printf '%s\n' "${fruits[@]}" | sort -f))
    echo "Case insensitive sort: ${case_sorted[*]}"
    
    # Sort strings (by length)
    local length_sorted=($(printf '%s\n' "${fruits[@]}" | 
        awk '{ print length($0), $0 }' | sort -n | cut -d ' ' -f 2-))
    echo "Sorted by length: ${length_sorted[*]}"
    
    # Sort numbers
    local sorted_nums=($(printf '%s\n' "${numbers[@]}" | sort -n))
    echo "Sorted numbers: ${sorted_nums[*]}"
    
    # Sort numbers (descending)
    local reverse_nums=($(printf '%s\n' "${numbers[@]}" | sort -nr))
    echo "Reverse sorted numbers: ${reverse_nums[*]}"
    
    # Custom sort (by second letter)
    second_letter_sort() {
        local arr=("$@")
        local sorted=($(printf '%s\n' "${arr[@]}" | 
            awk '{ print substr($0, 2, 1), $0 }' | sort | cut -d ' ' -f 2-))
        echo "${sorted[*]}"
    }
    
    local second_char_sorted=$(second_letter_sort "${fruits[@]}")
    echo "Sorted by second letter: $second_char_sorted"
}
```

### Associative Array Techniques

```bash
#!/usr/bin/env bash

# Check Bash version for associative array support
if (( BASH_VERSINFO[0] < 4 )); then
    echo "Associative arrays require Bash version 4 or higher" >&2
    exit 1
fi

# Initialize associative array
declare -A user_info=(
    [name]="John Smith"
    [email]="john@example.com"
    [age]=30
    [city]="New York"
)

# Basic operations
associative_basic() {
    local -A fruits=(
        [apple]="red"
        [banana]="yellow"
        [grape]="purple"
    )
    
    # Add or update elements
    fruits[orange]="orange"
    fruits[apple]="green"  # Update existing
    
    # Access element
    echo "Color of grape: ${fruits[grape]}"
    
    # Check if key exists
    if [[ -v fruits[cherry] ]]; then
        echo "Cherry exists in the array"
    else
        echo "Cherry does not exist in the array"
    fi
    
    # Get all keys
    echo "Keys: ${!fruits[@]}"
    
    # Get all values
    echo "Values: ${fruits[@]}"
    
    # Iterate over keys and values
    for fruit in "${!fruits[@]}"; do
        echo "$fruit is ${fruits[$fruit]}"
    done
    
    # Count elements
    echo "Number of fruits: ${#fruits[@]}"
    
    # Remove element
    unset 'fruits[banana]'
    echo "After removing banana: ${!fruits[@]}"
}

# Using associative arrays for counting
count_words() {
    local text=$1
    local -A word_counts
    
    # Split text into words and count occurrences
    for word in $text; do
        # Normalize word (lowercase, remove punctuation)
        word=$(echo "$word" | tr '[:upper:]' '[:lower:]' | tr -d '[:punct:]')
        
        # Skip empty words
        [[ -z "$word" ]] && continue
        
        # Increment count
        ((word_counts[$word]++))
    done
    
    # Sort by frequency (most frequent first)
    for word in "${!word_counts[@]}"; do
        echo "${word_counts[$word]} $word"
    done | sort -rn | head -10
}

# Associative array as a simple database
user_database() {
    local -A users=(
        [user1]="John:Smith:john@example.com:30"
        [user2]="Jane:Doe:jane@example.com:28"
        [user3]="Bob:Johnson:bob@example.com:45"
    )
    
    # Add a user
    add_user() {
        local id=$1
        local first_name=$2
        local last_name=$3
        local email=$4
        local age=$5
        
        users[$id]="$first_name:$last_name:$email:$age"
        echo "User added: $id"
    }
    
    # Get user details
    get_user() {
        local id=$1
        
        if [[ -v users[$id] ]]; then
            echo "User details for $id:"
            IFS=: read -r first last email age <<< "${users[$id]}"
            echo "  Name: $first $last"
            echo "  Email: $email"
            echo "  Age: $age"
        else
            echo "User not found: $id"
        fi
    }
    
    # Update user details
    update_user() {
        local id=$1
        local field=$2
        local value=$3
        
        if [[ ! -v users[$id] ]]; then
            echo "User not found: $id"
            return 1
        fi
        
        IFS=: read -r first last email age <<< "${users[$id]}"
        
        case "$field" in
            first) first="$value" ;;
            last)  last="$value" ;;
            email) email="$value" ;;
            age)   age="$value" ;;
            *)     echo "Invalid field: $field"; return 1 ;;
        esac
        
        users[$id]="$first:$last:$email:$age"
        echo "User updated: $id"
    }
    
    # Delete a user
    delete_user() {
        local id=$1
        
        if [[ -v users[$id] ]]; then
            unset 'users[$id]'
            echo "User deleted: $id"
        else
            echo "User not found: $id"
        fi
    }
    
    # List all users
    list_users() {
        echo "User list:"
        for id in "${!users[@]}"; do
            IFS=: read -r first last _ _ <<< "${users[$id]}"
            echo "  $id: $first $last"
        done
    }
    
    # Search users
    search_users() {
        local term=$1
        local found=0
        
        echo "Search results for '$term':"
        for id in "${!users[@]}"; do
            if [[ "${users[$id]}" == *"$term"* ]]; then
                IFS=: read -r first last email age <<< "${users[$id]}"
                echo "  $id: $first $last ($email)"
                ((found++))
            fi
        done
        
        echo "Found $found user(s)"
    }
    
    # Example usage
    add_user "user4" "Alice" "Williams" "alice@example.com" 33
    get_user "user2"
    update_user "user1" "age" 31
    list_users
    search_users "Johnson"
    delete_user "user3"
    list_users
}

# Using associative arrays for configuration
config_manager() {
    local config_file=$1
    local -A config
    
    # Load configuration
    load_config() {
        if [[ ! -f "$config_file" ]]; then
            echo "Config file not found: $config_file"
            return 1
        fi
        
        while IFS="=" read -r key value; do
            # Skip comments and empty lines
            [[ "$key" =~ ^[[:space:]]*# || -z "$key" ]] && continue
            
            # Trim whitespace
            key="${key#"${key%%[![:space:]]*}"}"
            key="${key%"${key##*[![:space:]]}"}"
            
            value="${value#"${value%%[![:space:]]*}"}"
            value="${value%"${value##*[![:space:]]}"}"
            
            # Store in associative array
            config["$key"]="$value"
        done < "$config_file"
        
        echo "Loaded ${#config[@]} configuration settings"
    }
    
    # Get configuration value
    get_config() {
        local key=$1
        local default=$2
        
        if [[ -v config[$key] ]]; then
            echo "${config[$key]}"
        elif [[ -n "$default" ]]; then
            echo "$default"
        else
            echo "Config key not found: $key" >&2
            return 1
        fi
    }
    
    # Set configuration value
    set_config() {
        local key=$1
        local value=$2
        
        config["$key"]="$value"
        echo "Updated configuration: $key=$value"
    }
    
    # Save configuration
    save_config() {
        local output=${1:-"$config_file"}
        
        # Create header
        cat > "$output" << EOF
# Configuration file
# Generated on $(date)
#

EOF
        
        # Sort keys and write key-value pairs
        for key in $(echo "${!config[@]}" | tr ' ' '\n' | sort); do
            echo "$key=${config[$key]}" >> "$output"
        done
        
        echo "Configuration saved to $output"
    }
    
    # Load initial configuration
    [[ -f "$config_file" ]] && load_config
    
    # Example usage
    echo "Current database host: $(get_config "db.host" "localhost")"
    set_config "db.host" "db.example.com"
    set_config "app.name" "My Application"
    save_config
}
```

## Bash Completion

### Basic Completion

```bash
#!/usr/bin/env bash

# Basic tab completion for a command
setup_basic_completion() {
    local cmd=$1
    
    # Define completion function
    _${cmd}_complete() {
        local cur=${COMP_WORDS[COMP_CWORD]}
        local prev=${COMP_WORDS[COMP_CWORD-1]}
        local opts="--help --version --verbose"
        
        # Handle options
        case "$prev" in
            --file|-f)
                # Complete with files
                COMPREPLY=($(compgen -f -- "$cur"))
                return 0
                ;;
            --dir|-d)
                # Complete with directories
                COMPREPLY=($(compgen -d -- "$cur"))
                return 0
                ;;
            --user|-u)
                # Complete with user names
                COMPREPLY=($(compgen -u -- "$cur"))
                return 0
                ;;
        esac
        
        # Complete with options if current word starts with -
        if [[ "$cur" == -* ]]; then
            COMPREPLY=($(compgen -W "$opts" -- "$cur"))
        else
            # Default completion (files and directories)
            COMPREPLY=($(compgen -f -d -- "$cur"))
        fi
        
        return 0
    }
    
    # Register completion function
    complete -F _${cmd}_complete "$cmd"
    
    echo "Basic completion set up for '$cmd'"
}

# Install completion for the current script
install_completion() {
    local script_name=$(basename "$0")
    local completion_dir="$HOME/.bash_completion.d"
    local completion_file="$completion_dir/$script_name"
    
    # Create completion directory if it doesn't exist
    mkdir -p "$completion_dir"
    
    # Generate completion script
    cat > "$completion_file" << EOF
#!/usr/bin/env bash

# Bash completion for $script_name

_${script_name}_complete() {
    local cur=\${COMP_WORDS[COMP_CWORD]}
    local prev=\${COMP_WORDS[COMP_CWORD-1]}
    local commands="install help version"
    local opts="--help --verbose --config"
    
    # Handle commands and options
    case "\$prev" in
        $script_name)
            # Complete with commands
            COMPREPLY=(\$(compgen -W "\$commands" -- "\$cur"))
            return 0
            ;;
        --config)
            # Complete with config files
            COMPREPLY=(\$(compgen -f -X '!*.conf' -- "\$cur"))
            return 0
            ;;
        install)
            # Complete with package names
            COMPREPLY=(\$(compgen -W "package1 package2 package3" -- "\$cur"))
            return 0
            ;;
    esac
    
    # Complete with options for current word starting with -
    if [[ "\$cur" == -* ]]; then
        COMPREPLY=(\$(compgen -W "\$opts" -- "\$cur"))
        return 0
    fi
    
    # Default completion
    COMPREPLY=(\$(compgen -f -- "\$cur"))
    return 0
}

# Register completion function
complete -F _${script_name}_complete $script_name
EOF
    
    # Make completion script executable
    chmod +x "$completion_file"
    
    # Source the completion script in bash_completion
    if [[ ! -f "$HOME/.bash_completion" ]]; then
        echo "# User bash completions" > "$HOME/.bash_completion"
    fi

    if ! grep -q "$completion_file" "$HOME/.bash_completion"; then
    echo "# Load completion for $script_name" >> "$HOME/.bash_completion"
    echo "[ -f \"$completion_file\" ] && source \"$completion_file\"" >> "$HOME/.bash_completion"
    fi

    echo "Completion installed for $script_name"
    echo "Restart your shell or run 'source $completion_file' to enable"

## Bash Completion (continued)

    if ! grep -q "$completion_file" "$HOME/.bash_completion"; then
        echo "# Load completion for $script_name" >> "$HOME/.bash_completion"
        echo "[ -f \"$completion_file\" ] && source \"$completion_file\"" >> "$HOME/.bash_completion"
    fi
    
    echo "Completion installed for $script_name"
    echo "Restart your shell or run 'source $completion_file' to enable"
}

### Advanced Completion

```bash
#!/usr/bin/env bash

# Dynamic completion based on available options
dynamic_completion() {
    local cmd=$1
    
    # Define completion function
    _${cmd}_complete() {
        local cur=${COMP_WORDS[COMP_CWORD]}
        local prev=${COMP_WORDS[COMP_CWORD-1]}
        local cmd_pos=1
        local subcmd=""
        
        # Find the subcommand position
        if [[ $COMP_CWORD -gt $cmd_pos ]]; then
            subcmd=${COMP_WORDS[cmd_pos]}
        fi
        
        # Complete based on subcommand
        case "$subcmd" in
            install)
                # Complete packages for install subcommand
                _complete_install
                ;;
            config)
                # Complete configuration options
                _complete_config
                ;;
            service)
                # Complete service commands
                _complete_service
                ;;
            *)
                # Complete subcommands
                if [[ $COMP_CWORD -eq $cmd_pos ]]; then
                    COMPREPLY=($(compgen -W "install config service help version" -- "$cur"))
                fi
                ;;
        esac
        
        return 0
    }
    
    # Completion function for install subcommand
    _complete_install() {
        local opts="--force --verbose --quiet"
        
        if [[ "$prev" == "install" ]]; then
            # Get available packages from a dynamic source
            local packages=$(get_available_packages)
            COMPREPLY=($(compgen -W "$packages" -- "$cur"))
        elif [[ "$cur" == -* ]]; then
            # Complete with options
            COMPREPLY=($(compgen -W "$opts" -- "$cur"))
        fi
    }
    
    # Completion function for config subcommand
    _complete_config() {
        local opts="--edit --list --reset"
        local configs="main.conf user.conf network.conf security.conf"
        
        if [[ "$prev" == "config" ]]; then
            # Complete with config actions
            COMPREPLY=($(compgen -W "$opts" -- "$cur"))
        elif [[ "$prev" == "--edit" ]]; then
            # Complete with config files
            COMPREPLY=($(compgen -W "$configs" -- "$cur"))
        elif [[ "$cur" == -* ]]; then
            # Complete with options
            COMPREPLY=($(compgen -W "$opts" -- "$cur"))
        fi
    }
    
    # Completion function for service subcommand
    _complete_service() {
        local actions="start stop restart status"
        local services=$(get_available_services)
        
        if [[ "$prev" == "service" ]]; then
            # Complete with service actions
            COMPREPLY=($(compgen -W "$actions" -- "$cur"))
        elif [[ " $actions " == *" $prev "* ]]; then
            # Complete with service names
            COMPREPLY=($(compgen -W "$services" -- "$cur"))
        fi
    }
    
    # Helper function to get available packages
    get_available_packages() {
        # This could query a package repository or read from a cache
        echo "package1 package2 package3 package4 package5"
    }
    
    # Helper function to get available services
    get_available_services() {
        # This could check systemd or other service manager
        echo "apache2 mysql nginx postgresql sshd"
    }
    
    # Register completion function
    complete -F _${cmd}_complete "$cmd"
    
    echo "Advanced completion set up for '$cmd'"
}

# Context-aware completion with caching
context_aware_completion() {
    local cmd=$1
    local cache_dir="$HOME/.cache/${cmd}_completion"
    
    # Create cache directory
    mkdir -p "$cache_dir"
    
    # Define completion function
    _${cmd}_complete() {
        local cur=${COMP_WORDS[COMP_CWORD]}
        local prev=${COMP_WORDS[COMP_CWORD-1]}
        
        # Check if we have a cached completion for this context
        local context_hash=$(echo "${COMP_WORDS[*]}" | md5sum | cut -d' ' -f1)
        local cache_file="$cache_dir/$context_hash"
        
        # Use cache if it exists and is fresh (less than 1 hour old)
        if [[ -f "$cache_file" && $(find "$cache_file" -mmin -60) ]]; then
            COMPREPLY=($(cat "$cache_file" | grep "^$cur" 2>/dev/null))
            return 0
        fi
        
        # Generate completions based on context
        local completions=""
        
        case "$prev" in
            --file)
                # Complete with files of specific types
                if command -v file >/dev/null 2>&1; then
                    # More selective file completion based on context
                    if [[ "${COMP_WORDS[*]}" == *"--image"* ]]; then
                        find . -type f -name "*$cur*" | 
                            xargs file | grep -i "image" | 
                            cut -d: -f1 > "$cache_file"
                    elif [[ "${COMP_WORDS[*]}" == *"--document"* ]]; then
                        find . -type f -name "*$cur*" | 
                            xargs file | grep -i "document\|text" | 
                            cut -d: -f1 > "$cache_file"
                    else
                        find . -type f -name "*$cur*" | head -100 > "$cache_file"
                    fi
                else
                    # Fallback to basic file completion
                    compgen -f -- "$cur" > "$cache_file"
                fi
                ;;
            --user)
                # Context-aware user completion
                if [[ "${COMP_WORDS[*]}" == *"--admin"* ]]; then
                    # Only show admin users
                    getent group sudo | cut -d: -f4 | tr ',' '\n' > "$cache_file"
                else
                    # Show all users
                    getent passwd | cut -d: -f1 > "$cache_file"
                fi
                ;;
            *)
                # Default options
                local opts="--file --user --help --verbose --image --document --admin"
                compgen -W "$opts" -- "$cur" > "$cache_file"
                ;;
        esac
        
        # Read completions from cache
        COMPREPLY=($(cat "$cache_file" 2>/dev/null))
        
        return 0
    }
    
    # Register completion function
    complete -F _${cmd}_complete "$cmd"
    
    echo "Context-aware completion with caching set up for '$cmd'"
}
```

## Real-World Scripts

### System Backup Script

```bash
#!/usr/bin/env bash
#
# system-backup.sh - Comprehensive system backup script
#
# This script creates a configurable backup of system directories
# and databases, with compression, encryption, and remote transfer options.
#
# Usage: ./system-backup.sh [options]
#   Options:
#     -c, --config FILE   Use custom config file
#     -f, --full          Perform full backup (default is incremental)
#     -d, --dry-run       Show what would be backed up without doing it
#     -v, --verbose       Enable verbose output
#     -h, --help          Show this help message
#
# Author: Your Name <your.email@example.com>
# License: MIT

# Exit on error, treat unset variables as error, fail on pipeline errors
set -euo pipefail

# Script configuration
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
SCRIPT_NAME="$(basename "${BASH_SOURCE[0]}")"
CONFIG_FILE="${SCRIPT_DIR}/backup.conf"
LOG_FILE="/var/log/system-backup.log"
BACKUP_DIR="/var/backups/system"
DATE_FORMAT="%Y-%m-%d_%H-%M-%S"
TIMESTAMP=$(date +"$DATE_FORMAT")
BACKUP_NAME="system-backup_${TIMESTAMP}"
EXCLUDE_FILE="${SCRIPT_DIR}/backup-exclude.list"
FULL_BACKUP=0
DRY_RUN=0
VERBOSE=0

# Default backup sources and targets
BACKUP_SOURCES=(
    "/etc"
    "/home"
    "/var/www"
    "/usr/local/bin"
)
BACKUP_MYSQL=1
BACKUP_POSTGRESQL=1
REMOTE_BACKUP=0
REMOTE_HOST=""
REMOTE_USER=""
REMOTE_PATH=""
ENCRYPT_BACKUP=0
GPG_RECIPIENT=""
RETENTION_DAYS=30

# Function to log messages
log() {
    local level=$1
    shift
    local message="$*"
    local timestamp=$(date +"$DATE_FORMAT")
    
    # Ensure log directory exists
    mkdir -p "$(dirname "$LOG_FILE")"
    
    echo "[$timestamp] [$level] $message" | tee -a "$LOG_FILE"
    
    # Send critical errors to admin
    if [[ "$level" == "CRITICAL" && -n "${ADMIN_EMAIL:-}" ]]; then
        echo "[$timestamp] [$level] $message" | mail -s "Backup Error: $SCRIPT_NAME" "$ADMIN_EMAIL"
    fi
}

# Function to display help
show_help() {
    cat <<EOF
Usage: $SCRIPT_NAME [options]

This script creates a configurable backup of system directories
and databases, with compression, encryption, and remote transfer options.

Options:
  -c, --config FILE   Use custom config file (default: $CONFIG_FILE)
  -f, --full          Perform full backup (default is incremental)
  -d, --dry-run       Show what would be backed up without doing it
  -v, --verbose       Enable verbose output
  -h, --help          Show this help message

Example:
  $SCRIPT_NAME --full --verbose

Configuration file format:
  BACKUP_DIR=/path/to/backup/dir
  BACKUP_SOURCES=(/etc /home /var/www)
  BACKUP_MYSQL=1
  BACKUP_POSTGRESQL=1
  REMOTE_BACKUP=0
  REMOTE_HOST=backup.example.com
  REMOTE_USER=backup
  REMOTE_PATH=/backups
  ENCRYPT_BACKUP=0
  GPG_RECIPIENT=user@example.com
  RETENTION_DAYS=30
  ADMIN_EMAIL=admin@example.com
EOF
}

# Function to parse command-line arguments
parse_args() {
    while [[ $# -gt 0 ]]; do
        case $1 in
            -c|--config)
                CONFIG_FILE="$2"
                shift 2
                ;;
            -f|--full)
                FULL_BACKUP=1
                shift
                ;;
            -d|--dry-run)
                DRY_RUN=1
                shift
                ;;
            -v|--verbose)
                VERBOSE=1
                shift
                ;;
            -h|--help)
                show_help
                exit 0
                ;;
            *)
                log "ERROR" "Unknown option: $1"
                show_help
                exit 1
                ;;
        esac
    done
}

# Function to read configuration file
read_config() {
    if [[ ! -f "$CONFIG_FILE" ]]; then
        log "WARNING" "Config file not found: $CONFIG_FILE. Using defaults."
        return
    fi
    
    log "INFO" "Reading configuration from $CONFIG_FILE"
    
    # Source the config file
    # shellcheck source=/dev/null
    source "$CONFIG_FILE"
    
    # Validate required settings
    if [[ ! -d "$BACKUP_DIR" && $DRY_RUN -eq 0 ]]; then
        log "INFO" "Creating backup directory: $BACKUP_DIR"
        mkdir -p "$BACKUP_DIR"
        chmod 700 "$BACKUP_DIR"
    fi
}

# Function to check required commands
check_dependencies() {
    local missing_deps=0
    local required_cmds=(
        "tar"
        "gzip"
        "rsync"
    )
    
    # Add conditional dependencies
    [[ $BACKUP_MYSQL -eq 1 ]] && required_cmds+=("mysqldump")
    [[ $BACKUP_POSTGRESQL -eq 1 ]] && required_cmds+=("pg_dump")
    [[ $REMOTE_BACKUP -eq 1 ]] && required_cmds+=("scp" "ssh")
    [[ $ENCRYPT_BACKUP -eq 1 ]] && required_cmds+=("gpg")
    
    log "INFO" "Checking for required commands..."
    
    for cmd in "${required_cmds[@]}"; do
        if ! command -v "$cmd" >/dev/null 2>&1; then
            log "ERROR" "Required command not found: $cmd"
            missing_deps=1
        elif [[ $VERBOSE -eq 1 ]]; then
            log "INFO" "Found required command: $cmd"
        fi
    done
    
    if [[ $missing_deps -eq 1 ]]; then
        log "CRITICAL" "Missing dependencies. Please install required packages."
        exit 1
    fi
}

# Function to create backup name with type
get_backup_filename() {
    local backup_type="incremental"
    [[ $FULL_BACKUP -eq 1 ]] && backup_type="full"
    
    echo "${BACKUP_NAME}_${backup_type}"
}

# Function to create directory backup
backup_directories() {
    local backup_file="${BACKUP_DIR}/$(get_backup_filename)_dirs.tar.gz"
    local tar_opts="-czf"
    
    # Add verbosity if requested
    [[ $VERBOSE -eq 1 ]] && tar_opts="${tar_opts}v"
    
    # Add exclude file if it exists
    local exclude_opt=""
    if [[ -f "$EXCLUDE_FILE" ]]; then
        exclude_opt="--exclude-from=$EXCLUDE_FILE"
        log "INFO" "Using exclude file: $EXCLUDE_FILE"
    fi
    
    # Use incremental backup if not full
    local incremental_opt=""
    if [[ $FULL_BACKUP -eq 0 ]]; then
        local snapshot_file="${BACKUP_DIR}/backup.snar"
        incremental_opt="--listed-incremental=$snapshot_file"
        log "INFO" "Performing incremental backup using snapshot: $snapshot_file"
        
        # Create snapshot file if it doesn't exist
        if [[ ! -f "$snapshot_file" && $DRY_RUN -eq 0 ]]; then
            touch "$snapshot_file"
        fi
    else
        log "INFO" "Performing full backup"
    fi
    
    log "INFO" "Backing up directories: ${BACKUP_SOURCES[*]}"
    log "INFO" "Backup will be saved to: $backup_file"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would run: tar $tar_opts $backup_file $exclude_opt $incremental_opt ${BACKUP_SOURCES[*]}"
    else
        tar $tar_opts "$backup_file" $exclude_opt $incremental_opt "${BACKUP_SOURCES[@]}" || {
            log "ERROR" "Directory backup failed"
            return 1
        }
        log "INFO" "Directory backup completed: $(du -h "$backup_file" | cut -f1)"
    fi
    
    echo "$backup_file"
}

# Function to backup MySQL databases
backup_mysql() {
    if [[ $BACKUP_MYSQL -eq 0 ]]; then
        log "INFO" "MySQL backup disabled in configuration"
        return
    fi
    
    local backup_file="${BACKUP_DIR}/$(get_backup_filename)_mysql.sql.gz"
    local mysql_opts="--single-transaction --routines --triggers --events"
    
    # Add verbosity if requested
    [[ $VERBOSE -eq 1 ]] && mysql_opts="$mysql_opts --verbose"
    
    log "INFO" "Backing up MySQL databases"
    log "INFO" "MySQL backup will be saved to: $backup_file"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would run: mysqldump $mysql_opts --all-databases | gzip > $backup_file"
    else
        if ! mysqldump $mysql_opts --all-databases | gzip > "$backup_file"; then
            log "ERROR" "MySQL backup failed"
            return 1
        fi
        log "INFO" "MySQL backup completed: $(du -h "$backup_file" | cut -f1)"
    fi
    
    echo "$backup_file"
}

# Function to backup PostgreSQL databases
backup_postgresql() {
    if [[ $BACKUP_POSTGRESQL -eq 0 ]]; then
        log "INFO" "PostgreSQL backup disabled in configuration"
        return
    fi
    
    local backup_file="${BACKUP_DIR}/$(get_backup_filename)_postgresql.sql.gz"
    local pg_opts="-Fc"  # Custom format for more flexible restoration
    
    # Add verbosity if requested
    [[ $VERBOSE -eq 1 ]] && pg_opts="$pg_opts -v"
    
    log "INFO" "Backing up PostgreSQL databases"
    log "INFO" "PostgreSQL backup will be saved to: $backup_file"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would run: pg_dumpall $pg_opts | gzip > $backup_file"
    else
        if ! pg_dumpall $pg_opts | gzip > "$backup_file"; then
            log "ERROR" "PostgreSQL backup failed"
            return 1
        fi
        log "INFO" "PostgreSQL backup completed: $(du -h "$backup_file" | cut -f1)"
    fi
    
    echo "$backup_file"
}

# Function to encrypt backup file
encrypt_backup() {
    local input_file=$1
    local output_file="${input_file}.gpg"
    
    if [[ $ENCRYPT_BACKUP -eq 0 ]]; then
        log "INFO" "Backup encryption disabled in configuration"
        return "$input_file"
    fi
    
    if [[ -z "$GPG_RECIPIENT" ]]; then
        log "ERROR" "GPG recipient not specified in configuration"
        return "$input_file"
    fi
    
    log "INFO" "Encrypting backup file: $input_file"
    log "INFO" "Encrypted backup will be saved to: $output_file"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would run: gpg --encrypt --recipient $GPG_RECIPIENT --output $output_file $input_file"
    else
        if ! gpg --encrypt --recipient "$GPG_RECIPIENT" --output "$output_file" "$input_file"; then
            log "ERROR" "Backup encryption failed"
            return "$input_file"
        fi
        
        # Remove the unencrypted file if encryption was successful
        if [[ -f "$output_file" ]]; then
            rm "$input_file"
            log "INFO" "Encryption completed: $(du -h "$output_file" | cut -f1)"
            return "$output_file"
        else
            log "ERROR" "Encrypted file not created"
            return "$input_file"
        fi
    fi
    
    echo "$output_file"
}

# Function to transfer backup to remote host
transfer_backup() {
    local file=$1
    
    if [[ $REMOTE_BACKUP -eq 0 ]]; then
        log "INFO" "Remote backup disabled in configuration"
        return
    fi
    
    if [[ -z "$REMOTE_HOST" || -z "$REMOTE_USER" || -z "$REMOTE_PATH" ]]; then
        log "ERROR" "Remote backup configuration incomplete"
        return
    fi
    
    log "INFO" "Transferring backup to ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_PATH}"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would run: scp $file ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_PATH}/"
    else
        if ! scp "$file" "${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_PATH}/"; then
            log "ERROR" "Remote transfer failed"
            return 1
        fi
        log "INFO" "Remote transfer completed"
    fi
}

# Function to clean up old backups
cleanup_old_backups() {
    if [[ $RETENTION_DAYS -le 0 ]]; then
        log "INFO" "Backup retention disabled (keeping all backups)"
        return
    fi
    
    log "INFO" "Cleaning up backups older than $RETENTION_DAYS days"
    
    local cutoff_date=$(date -d "-$RETENTION_DAYS days" +%s)
    local count=0
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would delete backups older than $RETENTION_DAYS days"
        return
    fi
    
    # Find and delete old backups
    while read -r backup_file; do
        local file_date=$(stat -c %Y "$backup_file")
        
        if [[ $file_date -lt $cutoff_date ]]; then
            log "INFO" "Deleting old backup: $backup_file"
            rm "$backup_file"
            ((count++))
        fi
    done < <(find "$BACKUP_DIR" -type f -name "system-backup_*" -not -path "*/.snapshot/*")
    
    log "INFO" "Deleted $count old backup files"
}

# Main function
main() {
    log "INFO" "Starting system backup process"
    
    # Parse command-line arguments
    parse_args "$@"
    
    # Read configuration
    read_config
    
    # Check dependencies
    check_dependencies
    
    # Create backup directory if it doesn't exist
    if [[ ! -d "$BACKUP_DIR" && $DRY_RUN -eq 0 ]]; then
        mkdir -p "$BACKUP_DIR"
        chmod 700 "$BACKUP_DIR"
    fi
    
    # Backup steps
    log "INFO" "Starting backup process: $(get_backup_filename)"
    
    # Backup directories
    local dir_backup=$(backup_directories)
    
    # Backup MySQL
    local mysql_backup=$(backup_mysql)
    
    # Backup PostgreSQL
    local postgresql_backup=$(backup_postgresql)
    
    # Encrypt backups if enabled
    if [[ $ENCRYPT_BACKUP -eq 1 ]]; then
        [[ -n "$dir_backup" ]] && dir_backup=$(encrypt_backup "$dir_backup")
        [[ -n "$mysql_backup" ]] && mysql_backup=$(encrypt_backup "$mysql_backup")
        [[ -n "$postgresql_backup" ]] && postgresql_backup=$(encrypt_backup "$postgresql_backup")
    fi
    
    # Transfer backups to remote host if enabled
    if [[ $REMOTE_BACKUP -eq 1 ]]; then
        [[ -n "$dir_backup" ]] && transfer_backup "$dir_backup"
        [[ -n "$mysql_backup" ]] && transfer_backup "$mysql_backup"
        [[ -n "$postgresql_backup" ]] && transfer_backup "$postgresql_backup"
    fi
    
    # Clean up old backups based on retention policy
    cleanup_old_backups
    
    log "INFO" "Backup process completed successfully"
}

# Call main function with all script arguments
main "$@"
```

### Log Monitoring and Analysis Script

```bash
#!/usr/bin/env bash
#
# log-monitor.sh - Advanced log monitoring and analysis script
#
# This script monitors log files for patterns, performs real-time analysis,
# and can send alerts when specific conditions are met.
#
# Usage: ./log-monitor.sh [options]
#   Options:
#     -c, --config FILE   Use custom config file
#     -f, --file FILE     Monitor specific log file
#     -p, --pattern STR   Search for specific pattern
#     -a, --alert         Enable alerting
#     -d, --daemon        Run as a daemon
#     -v, --verbose       Enable verbose output
#     -h, --help          Show this help message
#
# Author: Your Name <your.email@example.com>
# License: MIT

set -euo pipefail

# Script configuration
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
SCRIPT_NAME="$(basename "${BASH_SOURCE[0]}")"
CONFIG_FILE="${SCRIPT_DIR}/log-monitor.conf"
STATE_DIR="/var/lib/log-monitor"
LOG_FILE="/var/log/log-monitor.log"
ALERT_LOG="${STATE_DIR}/alerts.log"
TEMPFILE=$(mktemp)
PID_FILE="/var/run/log-monitor.pid"

# Default configuration
MONITOR_FILES=()
PATTERNS=(
    "ERROR"
    "FATAL"
    "CRITICAL"
    "exception"
    "fail"
    "denied"
)
IGNORE_PATTERNS=(
    "DEBUG"
    "test"
)
CHECK_INTERVAL=60
MAX_LINES=1000
ALERT_THRESHOLD=5
ALERT_WINDOW=300  # 5 minutes
ALERT_COOLDOWN=1800  # 30 minutes
ALERT_COMMAND=""
ALERT_EMAIL=""
RUN_AS_DAEMON=0
VERBOSE=0
ENABLE_ALERTS=0

# Function to log messages
log() {
    local level=$1
    shift
    local message="$*"
    local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    
    # Ensure log directory exists
    mkdir -p "$(dirname "$LOG_FILE")"
    
    echo "[$timestamp] [$level] $message" | tee -a "$LOG_FILE"
}

# Function to display help
show_help() {
    cat <<EOF
Usage: $SCRIPT_NAME [options]

This script monitors log files for patterns, performs real-time analysis,
and can send alerts when specific conditions are met.

Options:
  -c, --config FILE   Use custom config file (default: $CONFIG_FILE)
  -f, --file FILE     Monitor specific log file
  -p, --pattern STR   Search for specific pattern
  -a, --alert         Enable alerting
  -d, --daemon        Run as a daemon
  -v, --verbose       Enable verbose output
  -h, --help          Show this help message

Example:
  $SCRIPT_NAME --file /var/log/syslog --pattern "ERROR" --alert

Configuration file format:
  MONITOR_FILES=(/var/log/syslog /var/log/apache2/error.log)
  PATTERNS=("ERROR" "FATAL" "CRITICAL")
  IGNORE_PATTERNS=("DEBUG" "test")
  CHECK_INTERVAL=60
  MAX_LINES=1000
  ALERT_THRESHOLD=5
  ALERT_WINDOW=300
  ALERT_COOLDOWN=1800
  ALERT_COMMAND="notify-send 'Log Alert' 'Detected \$count matches for \$pattern in \$file'"
  ALERT_EMAIL="admin@example.com"
EOF
}

# Function to parse command-line arguments
parse_args() {
    while [[ $# -gt 0 ]]; do
        case $1 in
            -c|--config)
                CONFIG_FILE="$2"
                shift 2
                ;;
            -f|--file)
                MONITOR_FILES+=("$2")
                shift 2
                ;;
            -p|--pattern)
                PATTERNS+=("$2")
                shift 2
                ;;
            -a|--alert)
                ENABLE_ALERTS=1
                shift
                ;;
            -d|--daemon)
                RUN_AS_DAEMON=1
                shift
                ;;
            -v|--verbose)
                VERBOSE=1
                shift
                ;;
            -h|--help)
                show_help
                exit 0
                ;;
            *)
                log "ERROR" "Unknown option: $1"
                show_help
                exit 1
                ;;
        esac
    done
}

# Function to read configuration file
read_config() {
    if [[ ! -f "$CONFIG_FILE" ]]; then
        log "WARNING" "Config file not found: $CONFIG_FILE. Using defaults."
        return
    fi
    
    log "INFO" "Reading configuration from $CONFIG_FILE"
    
    # Source the config file
    # shellcheck source=/dev/null
    source "$CONFIG_FILE"
    
    # Create state directory if it doesn't exist
    if [[ ! -d "$STATE_DIR" ]]; then
        mkdir -p "$STATE_DIR"
        chmod 750 "$STATE_DIR"
    fi
}

# Function to check if script is already running
check_running() {
    if [[ -f "$PID_FILE" ]]; then
        local pid=$(cat "$PID_FILE")
        if kill -0 "$pid" 2>/dev/null; then
            log "ERROR" "Another instance is already running with PID $pid"
            exit 1
        else
            log "WARNING" "Stale PID file found, removing"
            rm -f "$PID_FILE"
        fi
    fi
}

# Function to run as daemon
run_as_daemon() {
    log "INFO" "Starting in daemon mode"
    
    # Create PID file
    echo $$ > "$PID_FILE"
    
    # Trap signals for clean exit
    trap 'log "INFO" "Received signal to stop"; rm -f "$PID_FILE" "$TEMPFILE"; exit 0' SIGINT SIGTERM
    
    # Main monitoring loop
    while true; do
        monitor_logs
        sleep "$CHECK_INTERVAL"
    done
}

# Function to check log files for patterns
monitor_logs() {
    log "INFO" "Checking log files for patterns"
    
    for file in "${MONITOR_FILES[@]}"; do
        if [[ ! -f "$file" ]]; then
            log "WARNING" "Log file not found: $file"
            continue
        fi
        
        if [[ $VERBOSE -eq 1 ]]; then
            log "INFO" "Monitoring file: $file"
        fi
        
        # Get file position from state file
        local position_file="${STATE_DIR}/$(echo "$file" | tr '/' '_').pos"
        local last_position=0
        
        if [[ -f "$position_file" ]]; then
            last_position=$(cat "$position_file")
        fi
        
        # Get current file size
        local current_size=$(stat -c %s "$file")
        
        # File was rotated if current size is smaller than last position
        if [[ $current_size -lt $last_position ]]; then
            log "INFO" "File $file was rotated, resetting position"
            last_position=0
        fi
        
        # Check for new content
        if [[ $current_size -eq $last_position ]]; then
            if [[ $VERBOSE -eq 1 ]]; then
                log "INFO" "No new content in $file"
            fi
            continue
        fi
        
        # Extract new lines
        if [[ $last_position -eq 0 ]]; then
            # First run or rotated file, just read the last MAX_LINES
            tail -n "$MAX_LINES" "$file" > "$TEMPFILE"
        else
            # Extract new content
            dd if="$file" bs=1 skip="$last_position" 2>/dev/null > "$TEMPFILE"
        fi
        
        # Update position
        echo "$current_size" > "$position_file"
        
        # Analyze new content
        analyze_log "$file" "$TEMPFILE"
    done
}

# Function to analyze log content
analyze_log() {
    local file=$1
    local content_file=$2
    
    for pattern in "${PATTERNS[@]}"; do
        # Skip empty patterns
        [[ -z "$pattern" ]] && continue
        
        # Check if pattern matches anything
        local matches=$(grep -c "$pattern" "$content_file" 2>/dev/null || echo "0")
        
        if [[ $matches -eq 0 ]]; then
            if [[ $VERBOSE -eq 1 ]]; then
                log "INFO" "No matches for pattern '$pattern' in $file"
            fi
            continue
        fi
        
        # Filter out ignored patterns
        local filtered_matches=$matches
        for ignore in "${IGNORE_PATTERNS[@]}"; do
            # Skip empty patterns
            [[ -z "$ignore" ]] && continue
            
            local ignored=$(grep -c "$pattern" "$content_file" | grep -c "$ignore" 2>/dev/null || echo "0")
            filtered_matches=$((filtered_matches - ignored))
        done
        
        if [[ $filtered_matches -le 0 ]]; then
            if [[ $VERBOSE -eq 1 ]]; then
                log "INFO" "All matches for pattern '$pattern' in $file were ignored"
            fi
            continue
        fi
        
        log "INFO" "Found $filtered_matches matches for pattern '$pattern' in $file"
        
        # Record the matches for alert analysis
        record_matches "$file" "$pattern" "$filtered_matches"
        
        # Check if alert threshold is exceeded
        if [[ $ENABLE_ALERTS -eq 1 ]]; then
            check_alert_threshold "$file" "$pattern"
        fi
    done
}

# Function to record matches for alert analysis
record_matches() {
    local file=$1
    local pattern=$2
    local count=$3
    local timestamp=$(date +%s)
    
    # Create a record in the matches log
    echo "$timestamp $file $pattern $count" >> "${STATE_DIR}/matches.log"
    
    # Purge old records
    local cutoff=$((timestamp - ALERT_WINDOW * 2))
    grep -v "^[0-9]\{10\} " "${STATE_DIR}/matches.log" > "${STATE_DIR}/matches.log.new"
    mv "${STATE_DIR}/matches.log.new" "${STATE_DIR}/matches.log"
}

# Function to check if alert threshold is exceeded
check_alert_threshold() {
    local file=$1
    local pattern=$2
    local timestamp=$(date +%s)
    local window_start=$((timestamp - ALERT_WINDOW))
    
    # Calculate total matches in the alert window
    local total_matches=0
    while read -r record_time record_file record_pattern record_count; do
        if [[ $record_time -ge $window_start && \
              "$record_file" == "$file" && \
              "$record_pattern" == "$pattern" ]]; then
            total_matches=$((total_matches + record_count))
        fi
    done < "${STATE_DIR}/matches.log"
    
    if [[ $total_matches -ge $ALERT_THRESHOLD ]]; then
        # Check if we're in cooldown period
        local alert_key="${file}_${pattern}"
        local last_alert_file="${STATE_DIR}/last_alert_${alert_key//\//_}"
        local can_alert=1
        
        if [[ -f "$last_alert_file" ]]; then
            local last_alert=$(cat "$last_alert_file")
            if [[ $((timestamp - last_alert)) -lt $ALERT_COOLDOWN ]]; then
                if [[ $VERBOSE -eq 1 ]]; then
                    log "INFO" "In cooldown period for $file/$pattern"
                fi
                can_alert=0
            fi
        fi
        
        if [[ $can_alert -eq 1 ]]; then
            trigger_alert "$file" "$pattern" "$total_matches"
            
            # Update last alert time
            echo "$timestamp" > "$last_alert_file"
        fi
    fi
}

# Function to trigger an alert
trigger_alert() {
    local file=$1
    local pattern=$2
    local count=$3
    local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    
    log "ALERT" "Threshold exceeded: $count matches for '$pattern' in $file"
    
    # Record the alert
    echo "[$timestamp] $file $pattern $count" >> "$ALERT_LOG"
    
    # Execute alert command if configured
    if [[ -n "$ALERT_COMMAND" ]]; then
        local cmd=$(eval echo "$ALERT_COMMAND")
        log "INFO" "Executing alert command: $cmd"
        eval "$cmd" &
    fi
    
    # Send email alert if configured
    if [[ -n "$ALERT_EMAIL" ]]; then
        log "INFO" "Sending alert email to $ALERT_EMAIL"
        
        local email_subject="Log Alert: $count matches for '$pattern' in $file"
        local email_body="
Log Monitoring Alert
--------------------
Time: $timestamp
File: $file
Pattern: $pattern
Count: $count matches in the last $((ALERT_WINDOW / 60)) minutes

Recent matches:
$(grep "$pattern" "$TEMPFILE" | tail -n 10)

This email was sent by the log-monitor script.
        "
        
        echo "$email_body" | mail -s "$email_subject" "$ALERT_EMAIL"
    fi
}

# Function to generate a report of log activity
generate_report() {
    local report_file="${STATE_DIR}/report_$(date +%Y%m%d).txt"
    local start_time=$(date "+%Y-%m-%d %H:%M:%S")
    
    log "INFO" "Generating log activity report to $report_file"
    
    cat > "$report_file" << EOF
Log Monitoring Report
====================
Generated: $start_time

Files Monitored
--------------
EOF
    
    for file in "${MONITOR_FILES[@]}"; do
        echo "- $file" >> "$report_file"
        
        # File statistics
        if [[ -f "$file" ]]; then
            local size=$(du -h "$file" | cut -f1)
            local last_modified=$(stat -c "%y" "$file")
            echo "  Size: $size, Last Modified: $last_modified" >> "$report_file"
        else
            echo "  (File not found)" >> "$report_file"
        fi
    done
    
    # Pattern matches summary
    cat >> "$report_file" << EOF

Pattern Matches (Last 24 Hours)
------------------------------
EOF
    
    local yesterday=$(date -d "yesterday" +%s)
    for pattern in "${PATTERNS[@]}"; do
        local pattern_count=0
        
        while read -r record_time record_file record_pattern record_count; do
            if [[ $record_time -ge $yesterday && "$record_pattern" == "$pattern" ]]; then
                pattern_count=$((pattern_count + record_count))
            fi
        done < "${STATE_DIR}/matches.log"
        
        echo "- '$pattern': $pattern_count matches" >> "$report_file"
    done
    
    # Recent alerts
    cat >> "$report_file" << EOF

Recent Alerts
------------
EOF
    
    if [[ -f "$ALERT_LOG" ]]; then
        tail -n 10 "$ALERT_LOG" >> "$report_file"
    else
        echo "No recent alerts found" >> "$report_file"
    fi
    
    log "INFO" "Report generated: $report_file"
}

# Function to clean up before exit
cleanup() {
    log "INFO" "Cleaning up and exiting"
    rm -f "$TEMPFILE"
    [[ -f "$PID_FILE" && $$ -eq $(cat "$PID_FILE") ]] && rm -f "$PID_FILE"
}

# Main function
main() {
    log "INFO" "Starting log monitoring process"
    
    # Parse command-line arguments
    parse_args "$@"
    
    # Read configuration
    read_config
    
    # Check if already running
    check_running
    
    # Set up trap for cleanup
    trap cleanup EXIT
    
    # Validate configuration
    if [[ ${#MONITOR_FILES[@]} -eq 0 ]]; then
        log "ERROR" "No log files specified for monitoring"
        exit 1
    fi
    
    log "INFO" "Monitoring ${#MONITOR_FILES[@]} log files for ${#PATTERNS[@]} patterns"
    
    # Run as daemon if requested
    if [[ $RUN_AS_DAEMON -eq 1 ]]; then
        run_as_daemon
    else
        monitor_logs
        
        # Generate report
        generate_report
    fi
    
    log "INFO" "Log monitoring process completed"
}

# Call main function with all script arguments
main "$@"
```

### Automated Deployment Script

```bash
#!/usr/bin/env bash
#
# deploy.sh - Automated application deployment script
#
# This script automates the deployment process for web applications,
# with support for multiple environments, rollback, and notifications.
#
# Usage: ./deploy.sh [options]
#   Options:
#     -e, --env ENV       Target environment (dev, staging, prod)
#     -v, --version VER   Version to deploy (tag or branch)
#     -r, --rollback      Rollback to previous version
#     -d, --dry-run       Simulate deployment without making changes
#     -f, --force         Force deployment without confirmation
#     --skip-tests        Skip running tests before deployment
#     --notify            Send notifications after deployment
#     -h, --help          Show this help message
#
# Author: Your Name <your.email@example.com>
# License: MIT

set -euo pipefail

# Script configuration
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
SCRIPT_NAME="$(basename "${BASH_SOURCE[0]}")"
CONFIG_FILE="${SCRIPT_DIR}/deploy.conf"
LOG_DIR="/var/log/deployments"
LOG_FILE="${LOG_DIR}/deploy_$(date +%Y%m%d_%H%M%S).log"
DEPLOYMENT_DIR="/var/www/deployments"
RELEASE_DIR="${DEPLOYMENT_DIR}/releases"
SHARED_DIR="${DEPLOYMENT_DIR}/shared"
CURRENT_SYMLINK="${DEPLOYMENT_DIR}/current"
REPO_URL=""
KEEP_RELEASES=5

# Default configuration
ENV="dev"
VERSION="main"
ROLLBACK=0
DRY_RUN=0
FORCE=0
SKIP_TESTS=0
SEND_NOTIFY=0
ENV_VARS=()
DEPLOY_HOOKS=()
SLACK_WEBHOOK=""
EMAIL_NOTIFY=""

# Function to log messages
log() {
    local level=$1
    shift
    local message="$*"
    local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    
    # Ensure log directory exists
    mkdir -p "$LOG_DIR"
    
    echo "[$timestamp] [$level] $message" | tee -a "$LOG_FILE"
}

# Function to display help
show_help() {
    cat <<EOF
Usage: $SCRIPT_NAME [options]

This script automates the deployment process for web applications,
with support for multiple environments, rollback, and notifications.

Options:
  -e, --env ENV       Target environment (dev, staging, prod)
  -v, --version VER   Version to deploy (tag or branch)
  -r, --rollback      Rollback to previous version
  -d, --dry-run       Simulate deployment without making changes
  -f, --force         Force deployment without confirmation
  --skip-tests        Skip running tests before deployment
  --notify            Send notifications after deployment
  -h, --help          Show this help message

Example:
  $SCRIPT_NAME --env prod --version v1.2.3 --notify

Configuration file format:
  # Global settings
  REPO_URL="git@github.com:username/repo.git"
  KEEP_RELEASES=5
  
  # Environment-specific settings
  ENV_DEV_DEPLOY_DIR="/var/www/dev"
  ENV_DEV_ENV_VARS=("APP_ENV=development" "DEBUG=true")
  
  ENV_STAGING_DEPLOY_DIR="/var/www/staging"
  ENV_STAGING_ENV_VARS=("APP_ENV=staging" "DEBUG=true")
  
  ENV_PROD_DEPLOY_DIR="/var/www/production"
  ENV_PROD_ENV_VARS=("APP_ENV=production" "DEBUG=false")
  
  # Notification settings
  SLACK_WEBHOOK="https://hooks.slack.com/services/xxx/yyy/zzz"
  EMAIL_NOTIFY="admin@example.com"
EOF
}

# Function to parse command-line arguments
parse_args() {
    while [[ $# -gt 0 ]]; do
        case $1 in
            -e|--env)
                ENV="$2"
                shift 2
                ;;
            -v|--version)
                VERSION="$2"
                shift 2
                ;;
            -r|--rollback)
                ROLLBACK=1
                shift
                ;;
            -d|--dry-run)
                DRY_RUN=1
                shift
                ;;
            -f|--force)
                FORCE=1
                shift
                ;;
            --skip-tests)
                SKIP_TESTS=1
                shift
                ;;
            --notify)
                SEND_NOTIFY=1
                shift
                ;;
            -h|--help)
                show_help
                exit 0
                ;;
            *)
                log "ERROR" "Unknown option: $1"
                show_help
                exit 1
                ;;
        esac
    done
    
    # Validate environment
    case "$ENV" in
        dev|staging|prod)
            # Valid environment
            ;;
        *)
            log "ERROR" "Invalid environment: $ENV (must be dev, staging, or prod)"
            exit 1
            ;;
    esac
}

# Function to read configuration file
read_config() {
    if [[ ! -f "$CONFIG_FILE" ]]; then
        log "ERROR" "Config file not found: $CONFIG_FILE"
        exit 1
    fi
    
    log "INFO" "Reading configuration from $CONFIG_FILE"
    
    # Source the config file
    # shellcheck source=/dev/null
    source "$CONFIG_FILE"
    
    # Set environment-specific variables
    local env_upper=$(echo "$ENV" | tr '[:lower:]' '[:upper:]')
    local deploy_dir_var="ENV_${env_upper}_DEPLOY_DIR"
    local env_vars_var="ENV_${env_upper}_ENV_VARS"
    local hooks_var="ENV_${env_upper}_DEPLOY_HOOKS"
    
    # Check if environment-specific deployment directory is set
    if [[ -n "${!deploy_dir_var:-}" ]]; then
        DEPLOYMENT_DIR="${!deploy_dir_var}"
        RELEASE_DIR="${DEPLOYMENT_DIR}/releases"
        SHARED_DIR="${DEPLOYMENT_DIR}/shared"
        CURRENT_SYMLINK="${DEPLOYMENT_DIR}/current"
    fi
    
    # Get environment-specific environment variables
    if [[ -n "${!env_vars_var:-}" ]]; then
        ENV_VARS=("${!env_vars_var}")
    fi
    
    # Get environment-specific deploy hooks
    if [[ -n "${!hooks_var:-}" ]]; then
        DEPLOY_HOOKS=("${!hooks_var}")
    fi
    
    # Validate required settings
    if [[ -z "$REPO_URL" ]]; then
        log "ERROR" "Repository URL not specified in configuration"
        exit 1
    fi
}

# Function to check dependencies
check_dependencies() {
    local missing_deps=0
    local required_cmds=(
        "git"
        "rsync"
    )
    
    log "INFO" "Checking for required commands..."
    
    for cmd in "${required_cmds[@]}"; do
        if ! command -v "$cmd" >/dev/null 2>&1; then
            log "ERROR" "Required command not found: $cmd"
            missing_deps=1
        fi
    done
    
    if [[ $missing_deps -eq 1 ]]; then
        log "ERROR" "Missing dependencies. Please install required packages."
        exit 1
    fi
}

# Function to prepare deployment directory
prepare_directories() {
    log "INFO" "Preparing deployment directories"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would create directories: $DEPLOYMENT_DIR, $RELEASE_DIR, $SHARED_DIR"
    else
        mkdir -p "$RELEASE_DIR" "$SHARED_DIR"
        
        # Create shared directories if not exists
        for dir in "logs" "storage" "config"; do
            if [[ ! -d "${SHARED_DIR}/${dir}" ]]; then
                mkdir -p "${SHARED_DIR}/${dir}"
                log "INFO" "Created shared directory: ${SHARED_DIR}/${dir}"
            fi
        done
    fi
}

# Function to get current release information
get_current_release() {
    if [[ ! -L "$CURRENT_SYMLINK" ]]; then
        log "INFO" "No current release found"
        return 1
    fi
    
    local current_path=$(readlink "$CURRENT_SYMLINK")
    local current_release=$(basename "$current_path")
    
    log "INFO" "Current release: $current_release"
    echo "$current_release"
}

# Function to rollback to previous release
perform_rollback() {
    log "INFO" "Performing rollback operation"
    
    local current_release=$(get_current_release || echo "")
    
    if [[ -z "$current_release" ]]; then
        log "ERROR" "Cannot rollback: No current release found"
        exit 1
    fi
    
    # Find the previous release
    local releases=($(find "$RELEASE_DIR" -mindepth 1 -maxdepth 1 -type d | sort -r))
    local previous_release=""
    local found_current=0
    
    for release in "${releases[@]}"; do
        if [[ $found_current -eq 1 ]]; then
            previous_release="$release"
            break
        fi
        
        if [[ "$(basename "$release")" == "$current_release" ]]; then
            found_current=1
        fi
    done
    
    if [[ -z "$previous_release" ]]; then
        log "ERROR" "Cannot rollback: No previous release found"
        exit 1
    fi
    
    local previous_release_name=$(basename "$previous_release")
    log "INFO" "Rolling back to previous release: $previous_release_name"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would change symlink from $current_release to $previous_release_name"
    else
        # Update the current symlink
        ln -sfn "$previous_release" "$CURRENT_SYMLINK"
        log "INFO" "Updated current symlink to point to: $previous_release_name"
        
        # Run post-rollback hooks
        run_deploy_hooks "post-rollback"
    fi
    
    log "INFO" "Rollback completed successfully"
}

# Function to clone repository
clone_repository() {
    local release_timestamp=$(date +%Y%m%d%H%M%S)
    local release_path="${RELEASE_DIR}/${release_timestamp}"
    
    log "INFO" "Creating new release: $release_timestamp"
    log "INFO" "Cloning repository: $REPO_URL (version: $VERSION)"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would clone $REPO_URL at version $VERSION to $release_path"
    else
        # Clone the repository
        git clone --depth 1 --branch "$VERSION" "$REPO_URL" "$release_path" || {
            log "ERROR" "Failed to clone repository"
            exit 1
        }
        
        # Remove .git directory to save space
        rm -rf "${release_path}/.git"
    fi
    
    echo "$release_path"
}

# Function to run tests
run_tests() {
    local release_path=$1
    
    if [[ $SKIP_TESTS -eq 1 ]]; then
        log "INFO" "Skipping tests (--skip-tests flag set)"
        return 0
    fi
    
    log "INFO" "Running tests"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would run tests in $release_path"
    else
        # Check for test script
        if [[ -f "${release_path}/run-tests.sh" ]]; then
            log "INFO" "Executing ${release_path}/run-tests.sh"
            
            # Run tests
            if ! (cd "$release_path" && bash run-tests.sh); then
                log "ERROR" "Tests failed"
                
                if [[ $FORCE -eq 0 ]]; then
                    log "INFO" "Deployment aborted due to test failure (use --force to deploy anyway)"
                    exit 1
                else
                    log "WARNING" "Proceeding with deployment despite test failures (--force flag set)"
                fi
            else
                log "INFO" "Tests passed successfully"
            fi
        else
            log "WARNING" "No test script found at ${release_path}/run-tests.sh"
        fi
    fi
}

# Function to create symbolic links for shared directories
create_symlinks() {
    local release_path=$1
    
    log "INFO" "Creating symbolic links for shared directories"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would create symbolic links for shared directories"
    else
        # Common shared directories
        for dir in "logs" "storage" "config"; do
            if [[ -d "${release_path}/${dir}" ]]; then
                rm -rf "${release_path}/${dir}"
                ln -sfn "${SHARED_DIR}/${dir}" "${release_path}/${dir}"
                log "INFO" "Created symlink for ${dir}"
            fi
        done
        
        # Project-specific .env file
        if [[ -f "${SHARED_DIR}/.env.${ENV}" ]]; then
            ln -sfn "${SHARED_DIR}/.env.${ENV}" "${release_path}/.env"
            log "INFO" "Created symlink for .env"
        fi
    fi
}

# Function to set environment variables
set_environment_variables() {
    local release_path=$1
    
    if [[ ${#ENV_VARS[@]} -eq 0 ]]; then
        log "INFO" "No environment variables to set"
        return
    fi
    
    log "INFO" "Setting environment variables"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would set environment variables: ${ENV_VARS[*]}"
    else
        # Create or update .env file
        local env_file="${release_path}/.env"
        
        for var in "${ENV_VARS[@]}"; do
            echo "$var" >> "$env_file"
            
            # Log variable name but not value for security
            local var_name=${var%%=*}
            log "INFO" "Set environment variable: $var_name"
        done
    fi
}

# Function to run deployment hooks
run_deploy_hooks() {
    local hook_type=$1
    
    if [[ ${#DEPLOY_HOOKS[@]} -eq 0 ]]; then
        log "INFO" "No deployment hooks configured"
        return
    fi
    
    log "INFO" "Running $hook_type hooks"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would run $hook_type hooks"
    else
        for hook in "${DEPLOY_HOOKS[@]}"; do
            local hook_cmd=""
            local hook_name=""
            
            # Parse hook definition (type:name:command)
            IFS=':' read -r hook_type_match hook_name hook_cmd <<< "$hook"
            
            # Skip hooks that don't match the requested type
            if [[ "$hook_type_match" != "$hook_type" ]]; then
                continue
            fi
            
            log "INFO" "Running $hook_type hook: $hook_name"
            
            # Execute hook command
            if ! eval "$hook_cmd"; then
                log "ERROR" "Hook $hook_name failed"
                
                if [[ $FORCE -eq 0 ]]; then
                    return 1
                else
                    log "WARNING" "Continuing despite hook failure (--force flag set)"
                fi
            fi
        done
    fi
    
    return 0
}

# Function to activate the new release
activate_release() {
    local release_path=$1
    local release_name=$(basename "$release_path")
    
    log "INFO" "Activating release: $release_name"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would activate release: $release_name"
    else
        # Update the current symlink
        ln -sfn "$release_path" "$CURRENT_SYMLINK"
        log "INFO" "Updated current symlink to point to: $release_name"
    fi
}

# Function to clean up old releases
cleanup_old_releases() {
    if [[ $KEEP_RELEASES -le 0 ]]; then
        log "INFO" "Keeping all releases (KEEP_RELEASES=$KEEP_RELEASES)"
        return
    fi
    
    log "INFO" "Cleaning up old releases (keeping $KEEP_RELEASES)"
    
    if [[ $DRY_RUN -eq 1 ]]; then
        log "DRY-RUN" "Would clean up old releases"
    else
        local releases=($(find "$RELEASE_DIR" -mindepth 1 -maxdepth 1 -type d | sort -r))
        
        if [[ ${#releases[@]} -le $KEEP_RELEASES ]]; then
            log "INFO" "No releases to clean up (have ${#releases[@]}, keeping $KEEP_RELEASES)"
            return
        fi
        
        # Remove excess releases
        for ((i=KEEP_RELEASES; i<${#releases[@]}; i++)); do
            local release="${releases[$i]}"
            log "INFO" "Removing old release: $(basename "$release")"
            rm -rf "$release"
        done
        
        log "INFO" "Removed $((${#releases[@]} - KEEP_RELEASES)) old releases"
    fi
}

# Function to send deployment notifications
send_notifications() {
    if [[ $SEND_NOTIFY -eq 0 ]]; then
        return
    fi
    
    log "INFO" "Sending deployment notifications"
    
    local deploy_info="Deployment to $ENV environment"
    if [[ $ROLLBACK -eq 1 ]]; then
        deploy_info="Rollback in $ENV environment"
    else
        deploy_info="$deploy_info (version: $VERSION)"
    fi
    
    local current_release=$(get_current_release || echo "unknown")
    deploy_info="$deploy_info\nCurrent release: $current_release"
    deploy_info="$deploy_info\nDeployed by: $(whoami) at $(date)"
    
    # Send Slack notification
    if [[ -n "$SLACK_WEBHOOK" ]]; then
        log "INFO" "Sending Slack notification"
        
        if [[ $DRY_RUN -eq 1 ]]; then
            log "DRY-RUN" "Would send Slack notification"
        else
            # Prepare Slack message
            local slack_message="{\"text\":\"$deploy_info\"}"
            
            # Send message
            curl -s -X POST -H "Content-type: application/json" \
                -d "$slack_message" "$SLACK_WEBHOOK" > /dev/null || {
                log "WARNING" "Failed to send Slack notification"
            }
        fi
    fi
    
    # Send email notification
    if [[ -n "$EMAIL_NOTIFY" ]]; then
        log "INFO" "Sending email notification to $EMAIL_NOTIFY"
        
        if [[ $DRY_RUN -eq 1 ]]; then
            log "DRY-RUN" "Would send email notification"
        else
            # Prepare email
            local email_subject="Deployment notification: $ENV environment"
            
            # Send email
            echo -e "$deploy_info" | mail -s "$email_subject" "$EMAIL_NOTIFY" || {
                log "WARNING" "Failed to send email notification"
            }
        fi
    fi
}

# Main function
main() {
    local start_time=$(date +%s)
    
    log "INFO" "Starting deployment process"
    
    # Parse command-line arguments
    parse_args "$@"
    
    # Read configuration
    read_config
    
    # Check dependencies
    check_dependencies
    
    # Display deployment information
    if [[ $ROLLBACK -eq 1 ]]; then
        log "INFO" "Performing rollback in $ENV environment"
    else
        log "INFO" "Deploying version $VERSION to $ENV environment"
    fi
    
    # Confirm deployment if not forced
    if [[ $FORCE -eq 0 && $DRY_RUN -eq 0 ]]; then
        read -p "Continue with deployment? (y/n) " -n 1 -r
        echo
        
        if [[ ! $REPLY =~ ^[Yy]$ ]]; then
            log "INFO" "Deployment aborted by user"
            exit 0
        fi
    fi
    
    # Prepare directories
    prepare_directories
    
    # Handle rollback if requested
    if [[ $ROLLBACK -eq 1 ]]; then
        perform_rollback
        send_notifications
        exit 0
    fi
    
    # Run pre-deployment hooks
    run_deploy_hooks "pre-deploy" || {
        log "ERROR" "Pre-deployment hooks failed"
        exit 1
    }
    
    # Clone repository
    local release_path=$(clone_repository)
    
    # Run tests
    run_tests "$release_path"
    
    # Create symbolic links
    create_symlinks "$release_path"
    
    # Set environment variables
    set_environment_variables "$release_path"
    
    # Run pre-activation hooks
    run_deploy_hooks "pre-activate" || {
        log "ERROR" "Pre-activation hooks failed"
        exit 1
    }
    
    # Activate the new release
    activate_release "$release_path"
    
    # Run post-deployment hooks
    run_deploy_hooks "post-deploy" || {
        log "WARNING" "Post-deployment hooks failed"
    }
    
    # Clean up old releases
    cleanup_old_releases
    
    # Send notifications
    send_notifications
    
    # Calculate execution time
    local end_time=$(date +%s)
    local execution_time=$((end_time - start_time))
    
    log "INFO" "Deployment completed successfully in $execution_time seconds"
}

# Call main function with all script arguments
main "$@"
```
