# fd

## First Attempting
After connecting with the server, first, we can use the command ```ls -l``` to list all files, subdirectories, and their detailed information.<br>
```c
-r-sr-x--- 1 fd_pwn fd   7322 Jun 11  2014 fd
-rw-r--r-- 1 root   root  418 Jun 11  2014 fd.c
-r--r----- 1 fd_pwn root   50 Jun 11  2014 flag
```
In the current directory, there are three files, where ```fd``` is readable and executable, ```fd.c``` is readable and writable, and ```flag``` is readable only. <br>

Then we execute the executable file ```fd```. It shows ```pass argv[1] a number```, which means to pass a number as the second parameter, ```argv[1]```. If we pass a random integer, the result will be ```learn about Linux file IO```. <br>

## Further Analyzing
For further analysis, we analyze the source code in ```fd.c```<br>
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
	if(argc<2){
		printf("pass argv[1] a number\n");
		return 0;
	}
	int fd = atoi( argv[1] ) - 0x1234;
	int len = 0;
	len = read(fd, buf, 32);
	if(!strcmp("LETMEWIN\n", buf)){
		printf("good job :)\n");
		system("/bin/cat flag");
		exit(0);
	}
	printf("learn about Linux file IO\n");
	return 0;

}
```
In the code, there are only one function ```main```, and inside the function, there are two branches. The first one says to pass a number to ```argv[1]```, and the second branch executes the ```system``` function to catch the flag. Thus the statements between these two branches and the condition statement in the second branch will be the keypoint.<br>
```c
int fd = atoi( argv[1] ) - 0x1234;
int len = 0;
len = read(fd, buf, 32);
if(!strcmp("LETMEWIN\n", buf))
	...
```
For these piece of codes above, the function ```atoi``` converts the string, ```argv[1]```, to an integer. And the value of ```fd``` is equal to the integer minus ```0x1234``` (4660). Then ```fd``` is passed as the first parameter of function ```read```.<br>

The description of function ```read``` in man page says that

```c
ssize_t read(int fildes, void *buf, size_t nbyte);
The read() function shall attempt to read nbyte bytes from the file associated with the open file descriptor, fildes, into the buffer pointed to by buf.
```

Also, each process has three standard file descriptors, 0 for standard input, 1 for standard output, and 2 for standard error. So we can make ```fd``` equal to 0 and input some special strings from the console to ```buf```. <br>

The final result will be that we execute ```./fd 4660``` and type ```LETMEWIN``` to catch the flag ```mommy! I think I know what a file descriptor is!!```.<br>

Here is a guess, if we analyze the source code outside the server, we may create a file with the content ```LETMEWIN```, and pass the file description of ```fd``` to catch the flag.