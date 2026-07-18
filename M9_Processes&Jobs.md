# Processes and Jobs

## Listing Processes
The ps command lists running processes, but the default usage only shows what is active in your current terminal window.
To see a complete view of every process running on the system, you should use ps -ef or ps aux, which display all processes along with their unique Process ID (PID) and the exact command used to launch them. If the command paths are too long and get cut off at the edge of your screen, you can append ww to the flags (like ps -efww) to force the output to wrap so you can read the full path.
I have once again renamed /challenge/run to a random filename, and this time made it so that you cannot ls the /challenge directory! But I also launched it, so you can find it in the running process list, figure out the filename, and relaunch it directly for the flag! Good luck!
Run ps -ef to list all running commands, find the one that says challenge, then run it.

## Killing Processes
Now, it’s time to terminate your first process! In this challenge, /challenge/run will refuse to run while /challenge/dont_run is running! You must find the dont_run process and kill it. If you fail, pwn.college will disavow all knowledge of your mission. Good luck.

**ps -ef | grep dont_run**

**kill [PID]**

## Killing Misbehaving Processes
This challenge requires you to clear a “jammed” communication pipe by identifying and terminating a disruptive background process that is flooding it with garbage data. You will use the process listing command to find the Process ID (PID) of the decoy, the kill command to stop it, and then you will read from the pipe while running the challenge program to capture the clean flag.

In this challenge, there’s a decoy process that’s hogging a critical resource — a named pipe (FIFO) at /tmp/flag_fifo into which (like in the Practicing Piping FIFO challenge) /challenge/run wants to write your flag. You need to kill this process.

First, find the Process ID (PID) of the program named /challenge/decoy: ps -ef

Next, terminate that process using its PID (replace 1234 with the number you found), start a background reader on the pipe so it doesn’t block, and finally run the solution:

**kill 1234**

**cat /tmp/flag_fifo & /challenge/run**

First of all what is a named pipe(FIFO)?
A named pipe (created at **/tmp/flag_fifo** in this case) is a special file used for communication between two different processes.

*It works on a First-In, First-Out (FIFO) basis.
*One process writes data into the pipe, and another process reads data out of it.
*The Block Rule: By default, if a process tries to write to a pipe, it will block (pause/freeze) until another process opens the pipe to read from it. Similarly, a reader will block until a writer appears.

The Problem: The Decoy Process
The challenge states that a program named /challenge/decoy is already running in the background. It has opened /tmp/flag_fifo and is constantly flooding it with "garbage data."

Because this decoy process is hogging the pipe, the main challenge program (/challenge/run) cannot successfully write the clean flag into it, or if it does, the flag gets corrupted/buried by the garbage data.

To solve this, you need to perform three major tasks: find the bad process, destroy it, and set up a clean reader to catch the flag.

## Step 1: Find the Target (ps -ef)
Before you can kill a process, you need to know its Process ID (PID), which is a unique number Linux assigns to every running program.

The command ps -ef lists every process currently running on the system:

ps: Process Status.
-e: Select all processes.
-f: Full-format listing (shows columns like UID, PID, PPID, and the exact command line string).

### What to look for:
When you run ps -ef, look through the output (specifically the rightmost column labeled CMD) for the line that says /challenge/decoy. Look at the second column (PID) on that same line to find its number (e.g., 1234).

## Step 2: Terminate the Decoy (kill 1234)
Once you have the PID, you use the kill command to send a termination signal to that specific process.

**kill <PID>**

(Replace <PID> with the actual number you found in Step 1).

This immediately stops the decoy process from writing garbage data to /tmp/flag_fifo, leaving the pipe clean and open for your use.

## Step 3: Listen and Execute (cat /tmp/flag_fifo & /challenge/run)
This final line uses a clever Linux trick to handle the "blocking" nature of pipes by combining two commands using &.

### Part A: cat /tmp/flag_fifo &
cat /tmp/flag_fifo tells the system to read whatever gets dumped into the pipe and print it to your screen.

The & symbol at the end is crucial. It tells Linux to run this cat command in the background.

Why? If you didn't use &, your terminal would freeze right there, waiting indefinitely for data to enter the pipe, and you wouldn't be able to type the next command. By pushing it to the background, the reader is actively listening, but your terminal stays free.

### Part B: /challenge/run
The space or semicolon allows you to execute the actual challenge binary.
Now that the decoy is dead and a reader (cat) is waiting in the background, /challenge/run can safely open /tmp/flag_fifo, write the real flag into it, and exit.
The moment /challenge/run writes to the pipe, the background cat process catches the data and prints your flag to the screen.

## Suspending Processes

You have learned to interrupt processes with Ctrl-C, but there are less drastic measures you can use to get your terminal back! You can suspend processes to the background with Ctrl-Z. In this level, we'll explore how this works and, in the next level, we'll figure out how to resume those suspended processes!

This level's run wants to see another copy of itself running and using the same terminal. How? Use the terminal to launch it, then suspend it, then launch another copy while the first is suspended!

### Step 1:
**/challenge/run**
### Step 2:
press **Ctrl + Z**
### Step 3:
again, **/challenge/run**
It should reveal you the flag!

## Resuming Processes
Usually, when you suspend processes, you'll want to resume them at some point. Otherwise, why not just terminate them? To resume processes, your shell provides the fg command, a builtin that takes the suspended process, resumes it, and puts it back in the foreground of your terminal.

Go try it out! This challenge's run needs you to suspend it, then resume it. Good luck!


### Step 1:
**/challenge/run**
### Step 2:
press **Ctrl + Z** it will make your suspend your process i.e. /challenge/run
### Step 3:
fg /challenge/run it will make the process "/challenge/run" a foreground process and you should get your flag!

## Background Processes

You've resumed processes in the foreground with the fg command. You can also resume processes in the background with the bg command! This will allow the process to keep running, while giving you your shell back to invoke more commands in the meantime.

This level's run wants to see another copy of itself running, not suspended, and using the same terminal. How? Use the terminal to launch it, then suspend it, then background it with bg and launch another copy while the first is running in the background!

### Step 1:
run the command **/challenge/run**
### Step 2:
suspend the command by pressing **Ctrl + Z**
### Step 3:
run the suspended program i.e. "/challenge/run" in background
**bg** /challenge/run
### Step 4:
launch another copy of "/challenge/run" process while one copy is still running in the background
**/challenge/run**

## Foregrounding Processes

Imagine that you have a backgrounded process, and you want to mess with it some more. What do you do? Well, you can foreground a backgrounded process with fg just like you foreground a suspended process! This level will walk you through that!

consider a process for e.g. /challenge/run running in the background and we want to foreground this process, for this:

**fg** /challenge/run

### Starting Backgrounded Processes
Of course, you don't have to suspend processes to background them: you can start them backgrounded right off the bat! It's easy; all you have to do is append a & to the command, like so:
Now it's your turn to practice! Launch /challenge/run backgrounded for the flag!

### Step 1:
run the /challenge/run process apprended by a "&" to the command
**/challenge/run &**
You should get your flag!

## Process Exit Codes
Every shell command, including every program and every builtin, exits with an exit code when it finishes running and terminates. This can be used by the shell, or the user of the shell (that's you!) to check if the process succeeded in its functionality (this determination, of course, depends on what the process is supposed to do in the first place).

You can access the exit code of the most recently-terminated command using the special ? variable (don't forget to prepend it with $ to read its value!):
In this challenge, you must retrieve the exit code returned by /challenge/get-code and then run /challenge/submit-code with that error code as an argument.

* **/challenge/get-code $?**
* **/challenge/submit-code 1234**
