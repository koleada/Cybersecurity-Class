Task 1: 

run printenv to in terminal to print enviornment variables

Task 2: 

Run as is  ./a.out >> output1.txt: 
```c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
extern char **environ;
void printenv(){
int i = 0;
	while (environ[i] != NULL) {
		printf("%s\n", environ[i]);
		i++;
	}
}
void main(){
	pid_t childPid;
	switch(childPid = fork()) {
		case 0: /* child process */
		printenv();
		exit(0);
		default: /* parent process */
		// printenv();
		exit(0);
	}
}
```
Then uncomment printenv() recompile and run again. ./a.out >> output2.txt

Both produce the same exact output. diff output1.txt output2.txt produces no output


Task 3 Env Vars and execve():

compile and run:
```c
#include <unistd.h>
extern char **environ;
int main()
{
char *argv[2];
argv[0] = "/usr/bin/env";
argv[1] = NULL;
execve("/usr/bin/env", argv, NULL); 
return 0;
}
```
makes program (env) to print env variables

change execve invoation to: execve("/usr/bin/env", argv, environ);

This program gets the enviornment variables from the third argument to execve. environ is the processes current enviornment variable list. 



Task 4 Env Vars and System:

Compilie and run: 
```c
#include <stdio.h>
#include <stdlib.h>
int main()
{
system("/usr/bin/env");
return 0 ;
}
```

This also prints enviornemnt variables, as system() uses execl() which calls execve and passes it the array of environment variables.




Task 5 Set-UID Programs:

Compile:

```c
#include <stdio.h>
#include <stdlib.h>
extern char **environ;
int main()
{
int i = 0;
while (environ[i] != NULL) {
printf("%s\n", environ[i]);
i++;
}
}
```

Make owner of the program root and make it a Set UID program. 

```
$ sudo chown root <program>
```
```
$ sudo chmod 4755 <program>
```

We set three random enviornment variables and when we ran the program we see all enviornment variables make it into the Set-UID program. 


Task 6 The PATH Environment Variable and Set-UID Programs: 

The Set-UID program below is supposed to execute the /bin/ls command; however, the programmer only uses the relative path for the ls command, rather than the absolute path:
```c
int main()
{
system("ls");
return 0;
}
```
Please compile the above program, change its owner to root, and make it a Set-UID program. Can
you get this Set-UID program to run your own malicious code, instead of /bin/ls? If you can, is your
malicious code running with the root privilege? Describe and explain your observations.


- The answer is kind of. On WSL linux nosuid is applied to / by defualt, so suid programs dont work. On most linux distros suid programs are allowed in some areas. If they are allowed u can just do:
export PATH=.:$PATH (in home directory)
make a file called 'ls' in your home directory and to it add:

#!/bin/sh
/bin/zsh

This will spawn a root shell effectively escalating privlidges. 


Task 7  The LD PRELOAD Environment Variable and Set-UID Programs:

• Make myprog a regular program, and run it as a normal user.
$ ./a.out
I am not sleeping!

• Make myprog a Set-UID root program, and run it as a normal user.
no output at all

• Make myprog a Set-UID root program, export the LD PRELOAD environment variable again in
the root account and run it.
$ ./a.out
I am not sleeping!

• Make myprog a Set-UID user1 program (i.e., the owner is user1, which is another user account),
export the LD PRELOAD environment variable again in a different user’s account (not-root user) and
run it.
Depends on linux version/type but typically there should be no output

The dynamic linker ignores LD PRELOAD for SUID programs. If program is privlidged it will ignore injected libraries, eg ignoring/unsetting LD Preload


Task 8 Invoking External Programs Using system() versus execve()

```c
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

int main(int argc, char *argv[])
{
	char *v[3];
	char *command;
	if(argc < 2) {
		printf("Please type a file name.\n");
		return 1;
	}
	v[0] = "/bin/cat"; v[1] = argv[1]; v[2] = NULL;
	command = malloc(strlen(v[0]) + strlen(v[1]) + 2);
	sprintf(command, "%s %s", v[0], v[1]);
	// Use only one of the followings.
	system(command);
	// execve(v[0], v, NULL);
	return 0 ;
}
```

1. YES, system() passes the input to /bin/sh meaning shell commands execute. So doing something like:

```bash
./a.out "file.txt; rm protectedFile.txt" 
```
will cat file.txt but then will proceed to remove protectedFile.txt, obvious command injection. 


2. Using execve will remove the vulnerability, it will just pass the user controller arg to v[0] which is /bin/cat so we can not execute shell commands. 



Task 9: Capability Leaking

```c
#include <stdlib.h>
#include <stdio.h>
#include <fcntl.h>
void main()
{
int fd;
char *v[2];
/* Assume that /etc/zzz is an important system file,
* and it is owned by root with permission 0644.
* Before running this program, you should create
* the file /etc/zzz first. */
fd = open("/etc/zzz", O_RDWR | O_APPEND);
if (fd == -1) {
printf("Cannot open /etc/zzz\n");
exit(0);
}
// Print out the file descriptor value
printf("fd is %d\n", fd);
// Permanently disable the privilege by making the
// effective uid the same as the real uid
setuid(getuid());
// Execute /bin/sh
v[0] = "/bin/sh"; v[1] = 0;
execve(v[0], v, 0);
}
```

Yes, this can be exploited to write to a protected file as a normal user. First, create /etc/zzz with nothing in it just for testing and give it permissions 644. Then compile, make it owned by root, make it setuid. Then run the program which spwans a shell. From there, we can do:
```bash
echo "hacked" > %3
```
We then exit the shell and cat /etc/zzz to see we changed the file. 
































