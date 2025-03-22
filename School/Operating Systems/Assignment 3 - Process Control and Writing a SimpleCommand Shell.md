Luke Fisher
M14058588

###### 1.1: Guided Programming Exercise: Modify HW_02_a.c to use ONLY pointer arithmetic (no array expressions) and no for loops to do the same thing HW_02_a.c does. Be sure that you understand how the code works and how the pointer arithmetic relates to the array expression form. Put your modified code into the PDF of answers you are to turn in (I.E. fill it in below this question IN THE WORKSHEET). Provide liberal comments to explain what your pointer arithmetic is computing. (5 points) 
![[Pasted image 20250220122839.png]]

###### 1.2 Guided Programming Exercise: Modify HW_02_c.c to use envp instead of environ. Be sure that you understand how the code works. Provide liberal comments to explain what your pointer arithmetic is computing. Paste your source code for this into your answer document.  Also, answer the following questions and explain your answers: 
![[Pasted image 20250220130758.png]]

- **WHERE in the process memory map do each of these variables exist at run time?**

    -  environ - *This goes onto the data segment*
    - envp - *This is stored on the stack*

- **WHERE in process memory do the actual strings containing the values of the environment variables exist at run time?** *This is also stored in the stack, see screenshot below*
	![[Pasted image 20250220140532.png]]
	Memory address of `envp` is contained in the stack. 

###### 1.3 Guided Programming Exercise: Answer the following questions and explain your answers:
- **Presume that the code is instantiated in a process and it has JUST returned from its own call to fork() and there is now a clone of that process that is itself “just about” to pick up execution. Where will the clone child pick up its execution? **

	*The child process picks up execution immediately after the fork. The parent process calls fork and then the child process and parent process start at the next command immediately after the fork*

- **How does the clone child know it’s a clone? How does the parent process know it is not a clone, or at least not the clone if just made by calling fork()?**

	*The fork function returns the PID of the child process in the parent and in the child it returns 0.	*

- **Compile and run the program from the command line. Scroll through the whole history of outputs and describe how that two processes (original and clone) seem to be writing to the same terminal.  Do you notice anything that is odd?  Explain why you might be seeing this oddness.**

	*The two processes seem to execute in a random order, bouncing between parent and child indeterminately. The CPU scheduler decides which process will execute their instructions thus the output is non-deterministic which creates "oddness". They write to the same terminal due to the child inheriting the parent's file descriptors.*

###### 1.4a Guided Programming Exercise: Answer the following questions and explain your answers:
- **How are the outputs for HW_02_e.c  and HW_02_d.c different? Explain, in your own words, why the output for HW_02_e.c appears as it does and must ALWAYS appear as it does.**

	*The previous example had no explicit wait function call. Therefore, the output multiplexing of the parent and child process is non-deterministic and is dependent on the CPU scheduler. However, the second example contains a wait function call which ensures that the child process exits before continuing the parent process execution. Because of this the output IS deterministic, the child will always print out all values of the counter before the parent does.*

- **What happens if a process that has no children calls wait(NULL) You may answer this question either using Internet research OR simply running some test code and examining the results.**

	*The program continues execution, however errno is written to with status code 10 which is `ECHILD` which means that there's no child process. And the function returns -1. The error is defined in `errno-base.h`*

- **Using only wait(NULL)calls and any standard C programing constructs you like, write a program that prompts the user the enter a number of children to be created. Your program should create that many children and each child should print a message that says “Hello from child process XXX” where XXX is the process ID of the child.  WHEN ALL OF THE CHILDREN FINISH the parent should then, and only then, print a message that says “Hello from parent process XXX” where XXX is the process ID of the parent.  Note your program should work for any reasonable request for processes. Your code should be fully commented and you should explain, in comments, what each line of code is doing and why it is doing it.  A functioning program for this question might, for example, print something like this:**

	![[Pasted image 20250221101142.png]]
	
	![[Pasted image 20250221101240.png]]

###### 1.4b Guided Programming Exercise:  Answer the following questions and explain your answers:
- **With regard to program HW_02_f.c, explain in your own words what each of these macros do:  WIFEXITED(return_status) and WEXITSTATUS(return_status)**

	*WIFEXITED is a comparison macro that tests whether or not a certain process status indicates that the process exited normally. A process exits normally by calling `exit(3)` or `_exit(2)`. The macro expands to this:* 
			*`(((return_status) & 0x7f) == 0)`*
	
	*WEXITSTATUS simply put returns the exit status of the child. More specifically, it returns the 8 least significant bits of the `status` variable which is either the value returned when calling `exit(3)` or `_exit(2)`, or it is the return value of the main function.*

- **Compile and run HW_02_f.c and, presuming your executable is in a.out, run this command from the shell prompt: ` ./a.out ; echo "Parent Terminated Normally with a return value of $?"`.**

	**Explain, in your own words, why you see the screen output you do and explain how that output relates to both the contents of the program AND the nature of the shell command used to invoke it.  Hint: This has everything to do with how processes "communicate" their exit statues to other processes.**

	*Breaking this down, first we see that there is a call to `./a.out`. Presumably this is the executable file to be executed. Then after that there is a semicolon. The semicolon is used to separate a chain of commands that will be executed sequentially without regard to the exit status. (Other separators such as `&&` and `||` only execute the next command based on the status of the previous command.) Finally, we echo a message onto the console. The message `"Parent Terminated Normally with a return value of"` is hardcoded. And finally, there's a variable `$?` which represents the status of the previous command. In this case, the assumption is that the process will terminate normally given the preceding message.*

###### 1.5 Guided Programming Exercise:  Answer the following questions and explain your answers:
- **Compile and run HW_02_g.c and, presuming your executable is in a.out, run this command from the shell prompt ./a.out ls -F   Explain, in your own words, why you see the screen output that you do and how that output related to both the content of the program AND the nature of the shell command used to invoke it. Be sure to comment on the pointer arithmetic done inside the line: `execvp(*(argv+1), argv+1)`;**

	*When running the script we see that it produces the output of `ls -F`. It accomplishes this by creating a forked child process and running `execvp(*(argv+1), argv+1)`. The first argument specifies the command to execute (the command is searched for in the directories in the PATH environment variable). The second specifies the list of arguments to pass into that command. The +1 pointer arithmetic increments past the first argument of the program. Normally, the first argument is the name of the executable itself, so in order to jump past `./a.out` you increment the argv pointer.*

- **Compile and run HW_02_g.c and, presuming your executable is in a.out, run this command from the shell prompt ./a.out   Explain, in your own words, why you see the screen output that you do and how that output related to both the content of the program AND the nature of the shell command used to invoke it. Be sure to comment on the pointer arithmetic done inside the line: `execvp(*(argv+1), argv+1)`;**

	*Essentially, what's happening is that `execvp` is getting called with NULL arguments. `*(argv+1)` points to the null terminating character that indicates the end of the arguments list. So this will lead to undefined behavior as you're passing null into a function that isn't expecting it. You should apply a check before calling execvp that ensures that `*(argv+1)` isn't NULL.*

- **Compile and run HW_02_g.c and, presuming your executable is in a.out, run this command from the shell prompt ./a.out hoopyfrood Explain, in your own words, why you see the screen output that you do and how that output related to both the content of the program AND the nature of the shell command used to invoke it. Be sure to comment on the pointer arithmetic done inside the line: `execvp(*(argv+1), argv+1)`; Of course, I’m assuming there is no executable file anywhere in your path called “hoopyfrood”.  If you happen to have an executable named “hoopyfrood” – then substitute a name that does NOT exist in your command path**

	*This triggers an error. Normally, execvp doesn't return anything unless there's an error. If there's an error, it will set errno and then return -1. Since `hoopyfrood` isn't a command that's listed in a directory within the PATH environment variable, it returns an error. The error in this case is `ENOENT` which means error, no entry. Once again the pointer arithmetic in the command is used to skip the name of the current executable file.*