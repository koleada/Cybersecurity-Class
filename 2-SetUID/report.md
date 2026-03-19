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


Task 6: 











































