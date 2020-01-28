# fd

## Viewing and Attampting
After connecting with the server, first we can use the command ```ls -l``` to list all files, subdirectories, and their detailed information.<br>
```
-r-sr-x--- 1 fd_pwn fd   7322 Jun 11  2014 fd
-rw-r--r-- 1 root   root  418 Jun 11  2014 fd.c
-r--r----- 1 fd_pwn root   50 Jun 11  2014 flag
```
In the current directory, there are three files, where ```fd``` is readable and executable, ```fd.c``` is readable and writable, and ```flag``` is readable only. <br>

Then we execute the executable file ```fd```. It shows ```pass argv[1] a number```, which means to pass a number as the second parameter. If we pass a random integer, the result will be ```learn about Linux file IO```. <br>

## Analysing
For further analysis, we view the source code in ```fd.c```<br>
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
In the code, there are only one function ```main```, and inside the function, there are two branches. The first one says to pass a number as the second parameter, and the second branch executes the ```system``` function to catch the flag. So the statements between these two branches will be the keypoint.<br>

```c
int fd = atoi( argv[1] ) - 0x1234;
int len = 0;
len = read(fd, buf, 32);
```
First, the function ```atoi``` converts the string, the second parameter, to an integer, and the value of ```fd``` will be the integer minus 0x1234 (4660). Then ```fd``` is passed as the first parameter of function ```read```.<br>

The description of ```read``` in man page says that
