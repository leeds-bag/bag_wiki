# Transferring data using SFTP


Author: Mathew Alexander

Acknowledgements: Information found at https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server . 



## What is SFTP?



Secure File Transfer Protocol (SFTP) is a protocol built in SSH that transfers files over a secure connection. This is the encrypted version of File Transfer Protocol (FTP), the use of which is now widely discouraged.



## Why would I use SFTP?



SFTP can be used to transfer files from any machine that has the SSH protocol (Windows, macOS, Linux and Unix). This can be useful if you use a remote machine (e.g. foe-Linux, JASMIN, etc.) and want to either move files between the remote machines or transfer files from your local machine to remote ones. 



## How do I use SFTP?



In either machine, first check that you can connect to the remote machine with SSH (e.g. foe-linux-01):

```
ssh username@foe-linux-cpu
```

n.b. You may need to proxy jump through <i>rash@leeds.ac.uk</i> if you're outside the university network.

If this works, you have verified that you are able to do sftp, you can exit with:


```
exit
```


Establish SFTP with the command:

```
sftp username@foe-linux-cpu
```


Now the terminal should look like this:


```
sftp> 
```


You can use the 'help' command to find out about the SFTP commands available:


```
sftp> help 
```


You can use most linux/unix commands here (e.g. ls, cd, pwd). By using 'l', you specify the command for the local machine:


```
sftp> ls

Desktop  Documents  Downloads  Folder1  Folder2
```


Once you have navigated to the desired directory on the remote machine, you can transfer a file from the local machine to the remote machine using the 'put' command:


```
sftp> put localFile
```


Upload an entire directory with the 'put -r' command:


```
sftp> put -r localFile
```


You can download files to the local machine using the 'get' command:


```
sftp> get localFile
```


You can copy the remote file to a different name by specifying the name afterwards:


```
sftp> get remoteFile localFile
```


Similarly with 'put', you can use option flags:

```
sftp> get -r remoteFile
```


Once you have transferred your files, you can exit with:


```
sftp> exit
```


More detail on other commands, such as file manipulation, setting up servers and error debugging can be found at: https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server