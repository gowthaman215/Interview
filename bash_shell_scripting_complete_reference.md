= Bash Shell Scripting — Complete Reference (Basic, Core, Advanced)
:toc:
:toclevels: 4
:sectnums:
:source-highlighter: coderay

== Introduction

Bash (Bourne Again SHell) is a Unix shell and command language.
It is widely used for:

* System automation
* Deployment scripts
* Monitoring and maintenance
* DevOps pipelines
* Daily Linux operations

Bash scripts execute commands sequentially in a shell environment.

---

== BASIC BASH CONCEPTS

== What Is a Shell?

A shell:
* Interprets user commands
* Interacts with the OS kernel
* Executes programs

Common shells:
* bash
* sh
* zsh
* fish

---

== First Bash Script

[source,bash]
----
#!/bin/bash
echo "Hello, Bash"
----

Make executable:
[source,bash]
----
chmod +x script.sh
./script.sh
----

---

== Variables

[source,bash]
----
name="Linux"
echo $name
----

Rules:
* No spaces around `=`
* Access using `$`

---

== Command Substitution

[source,bash]
----
date_now=$(date)
echo $date_now
----

---

== User Input

[source,bash]
----
read -p "Enter name: " name
echo "Hello $name"
----

---

== Positional Parameters

[source,bash]
----
echo $0   # script name
echo $1   # first argument
echo $@   # all arguments
----

---

== Exit Status

[source,bash]
----
ls /tmp
echo $?   # 0 = success
----

---

== CORE BASH CONCEPTS

== Conditional Statements

=== if / else

[source,bash]
----
if [ $x -gt 10 ]; then
    echo "Greater"
else
    echo "Smaller"
fi
----

---

== File Tests

[source,bash]
----
if [ -f file.txt ]; then
    echo "File exists"
fi
----

Common tests:
* `-f` file
* `-d` directory
* `-e` exists
* `-x` executable

---

== Case Statement

[source,bash]
----
case $1 in
    start) echo "Starting";;
    stop)  echo "Stopping";;
    *)     echo "Unknown";;
esac
----

---

== Loops

=== for Loop

[source,bash]
----
for i in 1 2 3; do
    echo $i
done
----

---

=== while Loop

[source,bash]
----
count=0
while [ $count -lt 5 ]; do
    ((count++))
done
----

---

== Functions

[source,bash]
----
greet() {
    echo "Hello $1"
}
greet "World"
----

---

== Arrays

[source,bash]
----
arr=(one two three)
echo ${arr[0]}
echo ${arr[@]}
----

---

== String Operations

[source,bash]
----
str="hello world"
echo ${#str}
echo ${str^^}
----

---

== Input / Output Redirection

[source,bash]
----
ls > out.txt
ls >> out.txt
ls 2> err.txt
----

---

== Pipes

[source,bash]
----
ps aux | grep root
----

Pipes connect command outputs to inputs.

---

== ADVANCED BASH CONCEPTS

== Quoting Rules

=== Double Quotes

[source,bash]
----
echo "$name"
----

Expands variables.

---

=== Single Quotes

[source,bash]
----
echo '$name'
----

Literal output.

---

== Subshells

[source,bash]
----
(cd /tmp && ls)
----

Runs in a child shell.

---

== Here Documents

[source,bash]
----
cat <<EOF
Line 1
Line 2
EOF
----

---

== Process Management

=== Background Jobs

[source,bash]
----
sleep 10 &
jobs
----

---

=== wait

[source,bash]
----
wait
----

Waits for background processes.

---

== Signal Handling

[source,bash]
----
trap "echo Interrupted" SIGINT
----

Handles Ctrl+C.

---

== Environment Variables

[source,bash]
----
export PATH=$PATH:/opt/bin
----

Available to child processes.

---

== Debugging Bash Scripts

=== Enable Debug Mode

[source,bash]
----
set -x
----

---

=== Exit on Error

[source,bash]
----
set -e
----

---

=== Treat Unset Variables as Error

[source,bash]
----
set -u
----

---

== Cron Jobs (Automation)

[source,bash]
----
crontab -e
----

Example:
[source,bash]
----
0 2 * * * /home/user/backup.sh
----

---

== Bash vs Shell (`sh`)

| bash | sh |
|----|----|
| Advanced features | Minimal |
| Arrays | No |
| Better scripting | Portable |

---

== Common Utilities Used in Bash

* grep
* awk
* sed
* cut
* sort
* xargs

---

== Example: Real Automation Script

[source,bash]
----
#!/bin/bash

LOG="/var/log/app.log"

if [ ! -f "$LOG" ]; then
    echo "Log not found"
    exit 1
fi

grep ERROR "$LOG" | tail -n 10
----

---

== Performance and Best Practices

* Quote variables
* Use functions
* Avoid unnecessary subshells
* Prefer `[[ ]]` over `[ ]`
* Check exit codes
* Keep scripts readable

---

== Common Interview Questions

* Difference between `$@` and `$*`
* `&&` vs `||`
* How to debug bash script?
* What is a subshell?
* Difference between `source` and `./script.sh`

---

== One-Line Takeaway

Bash scripting is a powerful tool for automating system tasks, orchestrating programs, and managing Linux environments efficiently.

