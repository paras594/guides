

# Bash Script Guide



**Create Script** 

```bash
#!/bin/bash

echo "Scripting is fun"
```



**Make script Executable and run**

```bash
chmod 755 script1.sh

./script.sh
```



**Creating variables**

```bash
#!/bin/bash

MY_SHELL="bash"

echo "I like $MY_SHELL shell"

# or

echo "I like the ${MY_SHELL} shell"

# assign output of a a command in variable
SERVER_NAME = ${hostname}
```



**Tests**

To create a test, place a conditional expression between brackets. The syntaxis: `[ condition-to-testfor ]` . You can test for several types of situations. For example, you can compare if strings are equal, if a number is greater than another one, or if a file exists. This test checks to see if /etc/passwd exists. If it does, it returns true. I.e., the command exits with a status of 0. If the file doesn't exist it returns false. I.e., the command exits with a status of 1.

```bash
[-e /etc/passwd] # checks if file exists

```



```
File operators:
-d FILE True if file is a directory.
-e FILE True if file exists.
-f FILE True if file exists and is a regular file.
-r FILE True if file is readable byyou.
-s FILE True if file exists and is not empty.
-w FILE True if the file is writable by you.
-x FILE True if the file is executable by you.

String operators:
-z STRING True if string is empty.
-n STRING True if string is not empty.
STRING True if string is not empty.
STRING1 = STRING2
True if the strings are equal.
STRING1 != STRING2
True if the strings are not equal.

Arithmetic operators:
arg1 –eq arg2 True if arg1 is equal to arg2.
arg1 –ne arg2 True if arg1 is not equal to arg2.
arg1 –lt arg2 True if arg1 is less than arg2.
arg1 –le arg2 True if arg1 is less than or equal to arg2.
arg1 –gt arg2 True if arg1 is greater than arg2.
arg1 –ge arg2 True if arg1 is greater than or equal to arg2.
```



**If statements**

```bash
if [ condition-true ]
then
	# command 1
	# command 2
elif [ condition-true ]
then
	# command 3
else 
	# command 4
fi

# or

if [ condition-true ]; then
	# command 1
fi
```



**For loop**

```bash
COLORS = "red green blue"
for COLOR in $COLORS; do
	echo "color: $COLOR"
done

# C style loop
for ((i=0; i<$LENGTH; i++)); do

# range loop
for i in {1..5}; do

# sequence loop
for i in $(seq 1 $LENGTH); do
	echo $i
done
```



**Positional parameters**

```bash
script.sh param1 param2 param3

# $0 : script.sh
# $1 : param1
# $2 : param2
```



**Take input from user**

```bash
# read -p "PROMPT MSG" VARIABLE
read –p "Enter a user name: " USER
```



**Exit status and return codes**

```bash
ls /not/here
echo "$?" # holds the return status of previous command
# 0 means success
# greater than 0 means error

# we can also give exit codes
exit 1
exit 0
```



**Creating Function**

```bash
function hello() {
	echo "hello !"
}

# call a function
hello

# function with params
function hello() {
	for NAME in $@; do
		echo "Hello $NAME"
	done
}

hello Jason Dan Ryan # parameters

# function with return
function hello() {
	return 5
}
hello
echo $ #prints the return value of previous function
```



**Variables Scopes**

By default all variables are global. Variables must be declared before they are used.

We can create local variable inside a function. Use local once then use it normally

```bash
function hello() {
	local var1='my local variable'
}
```



**Switch Case**

```bash
case "$1" in
  start)
    /tmp/sleep-walking-server &
    ;;
  stop)
    kill $(cat /tmp/sleep-walking-server.pid)
    ;;
  *)
    echo "Usage: $0 start|stop"
    exit 1
esac
```



**Logger**

Facilities are used to indicate what type of program or what part of the system the message originated from.

```
num, keyword, description
0 kern kernel messages
1 user user-level messages
2 mail mail system
3 daemon system daemons
4 auth security/authorization messages
5 syslog messages generated bysyslogd
6 lpr line printer subsystem
7 news network news subsystem
8 uucp UUCP subsystem
9 clock daemon
10 authprivsecurity/authorization messages
11 ftp FTP daemon
12 - NTP subsystem
13 - log audit
14 - log alert
15 cron clock daemon
16 local0 local use 0 (local0)
17 local1 local use 1 (local1)
18 local2 local use 2 (local2)
19 local3 local use 3 (local3)
20 local4 local use 4 (local4)
21 local5 local use 5 (local5)
22 local6 local use 6 (local6)
23 local7 local use 7 (local7)
```



The severities are emergency, alert, critical, error, warning, notice, info, and debug. The most severe message is an emergency message and the least severe message is a debugging message

```
Code Severity Keyword Description
0 Emergency emerg (panic) System is unusable
1 Alert alert Action must be taken
immediately
2 Critical crit Critical conditions
3 Error err (error) Error conditions
4 Warning warning (warn) Warning conditions
5 Notice notice Normal but
significant condition
6 Info info Informational
messages
7 Debug debug Debug-level messages
```



**Logger utility**

The logger command generates syslog messages. In its simplest form you simplysupply a message to the logger utility. By default, the logger utilitycreates messages using the user facility and the notice severity

logs can be found in:

- /var/log/messages
- /var/log/syslog

```bash
logger "message" # default

# specify facility.severity
logger -p local0.info "Message"

# adding tag (mostly script name)
logger -t myscript -p local0.info "message"

# include pid
logger -i -t myscript "message"

# print to screen as well
logger -s "message"
```





**Notes**

- give space after if 
- do not give space between variable and equals in var declaration
- The first line in a shell script should start with a shebang and the path to the interpreter that should be used to execute the script.
- To assign a value to a variable, start with the variable name followed by an equals sign followed by the value. Do not use a space before or after the equals sign.
- You can access the value stored in a variable by using $VARIABLE_NAME or ${VARIABLE_NAME} . The latter form is required if you want to precede or follow the variable with additional data.
- To assign the output of a command to a variable, enclose the command in parentheses and precede it with a dollar sign. VARIABLE_NAME=$(command)
- 