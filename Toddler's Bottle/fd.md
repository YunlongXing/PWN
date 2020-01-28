# fd

连接服务器后，使用```ls -l```命令查看目录下文件和子目录的详细信息<br>
```
-r-sr-x--- 1 fd_pwn fd   7322 Jun 11  2014 fd
-rw-r--r-- 1 root   root  418 Jun 11  2014 fd.c
-r--r----- 1 fd_pwn root   50 Jun 11  2014 flag
```
当前目录只有三个文件，其中```fd```可读可执行，```fd.c```可读可写，```flag```仅可读。<br><br>

直接执行```fd```，显示```pass argv[1] a number```，即需要传入一个数字作为第二个参数。随意传入一个整数，执行结果为```learn about Linux file IO```
