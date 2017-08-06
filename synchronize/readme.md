# WinSCP - Synchronize files from a local path to a remote drive
> Copy files/folders from a local disk to a remote drive

The WinSCP FTP client ([website](https://winscp.net/eng/index.php)) allow to run a script that make possible automation.

The idea under the script below is to connect to a FTP server and copy an entire directory from a local path (like c:\my documents f.i.) to a remove drive (like /backup/documents)

This script is a nice companion if you've f.i. a Synology at home and if you want to copy files/folders easily from your HDD so you can have a backup of them

## The script

```
# Automatically abort script on errors
# or choose "option batch continue" to ignore errors like "file access denied" and continue the script
option batch abort

# Disable overwrite confirmations that conflict with the previous
option confirm off

# Connect; **change this value for your own configuration**
open ftp://USERNAME:PASSWORD@HOST_OR_IP:PORT/

# Force binary mode transfer
option transfer ascii

# --------------------------------------------------------------------------------------------
# Synchronize the folder "C:\Christophe\" (local) to /Christophe (remote)
#        **change these values for your own configuration**
#
# Get all files / folders from the local drive and make a copy to the FTP drive.
#
# --> Copy new files / new foldres
# --> Delete files / folders not anymore on local drive from the remote drive

lcd "C:\Christophe"
cd /Christophe
synchronize remote

# ------------------------------------
# The end

close

exit
```

## How to use

*WinSCP is for Windows, this step by step explain how to use it on a Windows computer.*

1. Get a copy of the script : [download it](https://raw.githubusercontent.com/cavo789/winscp/master/synchronize/winscp_Synchronize.txt.txt) and save it, f.i., as  `c:\temp\ftp\winscp_Synchronize.txt.txt` (or update the script, see 2.1)
2. Edit the script and specify :
   1. in case of need, replace `ftp`by `sftp` (line 9)
   2. `USERNAME` : the FTP username (line 9)
   3. `PASSWORD` : the password associated to this account (line 9)
   4. `HOST_OR_IP` : the FTP host name or his IP (line 9)
   5. `PORT` : the port to use (21 for a FTP connexion f.i., line 9)
   6. The local folder where your files are stored (line 23)
   7. The remote folder from where the files should be copied (line 24), can be the ftp root or any subfolder
3. Start a DOS session (start -> run -> cmd.exe)
4. Go to your `c:\temp\ftp` folder : type `cd \temp\ftp`
5. Run winscp.com from there : type `"c:\program files (86)\WinSCP\WinSCP.com" /script="c:\temp\ftp\winscp_Synchronize.txt"`

If everything is correctly set up, winscp will start a session terminal and will start the synchronization.

## More info
More info about the Synchronize verb of WinSCP : https://winscp.net/eng/docs/scriptcommand_synchronize

* If files/folders are already there, don't do anything.
* If there are new files/folders, copy them.
* If files/folders are no more on the local drive, remove them from the remote server.

So local is the master.
