# WinSCP

![Banner](./banner.svg)

> Tips and tricks and small scripts for automation of the WinSCP FTP client (Windows).

<!-- table-of-contents - start -->
* [Tips](#tips)
    * [Start PuTTY without typing a password](#start-putty-without-typing-a-password)
    * [Retrieve password](#retrieve-password)
    * [Search for files that contain a keyword](#search-for-files-that-contain-a-keyword)
    * [Super User - Running command with SUDO SU](#super-user-running-command-with-sudo-su)
* [Scripts](#scripts)
    * [Download files with specific extension recursively](#download-files-with-specific-extension-recursively)
       * [The script](#the-script)
       * [How to use](#how-to-use)
    * [Synchronize files from a local path to a remote drive](#synchronize-files-from-a-local-path-to-a-remote-drive)
       * [The script](#the-script)
       * [How to use](#how-to-use)
       * [More info](#more-info)
          * [Local / Remote / Both](#local-remote-both)
    * [VBA - Visual Basic for Application](#vba-visual-basic-for-application)
* [License](#license)
<!-- table-of-contents - end -->

## Tips

### Start PuTTY without typing a password

We can start a PuTTY console without typing the SSH password but just reuse the one of the FTP connection.

![PuTTY](./010-tips/PuTTY/images/WinSCP_Putty.png)

1. open your `Preferences`
2. in the `Integration`->`Application` tab (1) be sure to check `Remember session password and pass it to PuTTY` (2).

This way, you just need to provide the login name and that’s it.

![Remember password](./010-tips/PuTTY/images/WinSCP_Putty-RememberPassword.png)

### Retrieve password

![Retrieve password](./010-tips/Retrieve-password/images/Retrieve_password.png)

To retrieve an encrypted password with WinSCP, you'll need to enable the logging (see 1 and 2) and enable storing password (3).

1. Click on the `Report` menu item
2. Click on the `Enable session saving`
3. Click on the `Save password`

A file will then be created in the `%TEMP%` folder. His name will be the session name.

By opening that `.log` file in Notepad, you'll retrieve the password un-encrypted, in a plain-text format.

### Search for files that contain a keyword

It is possible to launch a search to, for example, find the string *Notes management* in all the `.php` files of the remote site.

To do this, go to the `Commands` menu, then `Static custom commands` and finally `Search for Text...`.

It is, in fact, the execution of a script (an extension in the WinSCP language) which is here : `C:\Program Files (x86)\WinSCP\Extensions\SearchText.WinSCPextension.ps1`

![Search for Text...](./010-tips/Search-for-text/images/search_for_text_1.png)

The search result is displayed in a Powershell console:

![Console output](./010-tips/Search-for-text/images/search_for_text_2.png)

### Super User - Running command with SUDO SU

Due to the configuration done on the FTP server, it can happen that we can't create a folder or just uploading files by using a FTP like `WinSCP` even when we can do this by using a `SSH` connection (and by running a `sudo su` command).

`WinSCP` gives us a way to *force* such command:

* In the `Site Manager` window, click on the `Advanced` button

  ![Site Manager](./010-tips/sudo-Super-user/images/winscp_sudo1.png)

* Then go to `Environment` -> `SCP/Shell` and type `sudo su -` in the `Shell` entry

  ![sudo su -](./010-tips/sudo-Super-user/images/winscp_sudo2.png)

Try again and now, it should work.

## Scripts

### Download files with specific extension recursively

> How to quickly get all php scripts under /images? This script given the answer

The WinSCP FTP client ([website](https://winscp.net/eng/index.php)) allow to run a script that make possible automation.

The idea under the script below is to connect to a FTP server, go to the ftp root folder and then, recursively, get all files having the php extensions.

I've coded this small script when facing the following problem: there were a lot of viruses in the /images folder of a website and that specific folder has hundreds of sub-folders and the `totalsize` of `/images` was bigger than 1 GB.

#### The script

```text
option batch abort

option confirm off

lcd "c:\temp\ftp"

open ftp://USERNAME:PASSWORD@HOST_OR_IP/

cd /public_html/site_name/images

option transfer ascii

get -filemask:*.php *

close

exit
```

Change the file extension in `get -filemask:*.php *` to the desired extension.

#### How to use

*WinSCP is for Windows, this step by step explain how to use it on a Windows computer.*

1. Get a copy of the script: [download it](https://raw.githubusercontent.com/cavo789/winscp/master/download_recursive/rget.txt) and save it, f.i., as `c:\temp\ftp\rget.txt` (or update the script, see 2.1)
2. Edit the script and specify:
   1. where files should be downloaded, local folder (line 8)
   2. in case of need, replace `ftp`by `sftp` (line 11)
   3. `USERNAME`: the FTP username (line 11)
   4. `PASSWORD`: the password associated to this account (line 11)
   5. `HOST_OR_IP`: the FTP host name or his IP (line 11)
   6. The remote folder from where the files should be downloaded (line 14), can be the ftp root or any sub-folder
   7. the file extension (if not `.php`) (line 20)
   8. save the script
3. Start a DOS session (start -> run -> cmd.exe)
4. Go to your `c:\temp\ftp` folder: type `cd \temp\ftp`
5. Run `winscp.com` from there: type `"c:\program files (86)\WinSCP\WinSCP.com" /script="c:\temp\ftp\rget.txt"`

If everything is correctly set up, WinSCP will start a session terminal and will start to download each `.php` files found under your remote folder (sub-folders included).

### Synchronize files from a local path to a remote drive

> Copy files/folders from a local disk to a remote drive

The WinSCP FTP client ([website](https://winscp.net/eng/index.php)) allow to run a script that make possible automation.

The idea under the script below is to connect to a FTP server and copy an entire directory from a local path (like c:\my documents f.i.) to a remove drive (like /backup/documents)

This script is a nice companion if you've f.i. a Synology at home and if you want to copy files/folders easily from your HDD so you can have a backup of them

#### The script

Synchronize the folder `C:\Christophe\` (local) to `/Christophe` (remote)

* Get all files/folders from the local drive and make a copy to the FTP drive.
  * Copy new files / new folders
  * Delete files / folders not anymore on local drive from the remote drive
* `-filemask="|.git/` for ignoring the `.git` folder.

```text
option batch abort

option confirm off

open ftp://USERNAME:PASSWORD@HOST_OR_IP:PORT/

option transfer ascii

lcd "C:\Christophe"
cd /Christophe

synchronize remote -preservetime -transfer=automatic -filemask="|.git/"

close

exit
```

#### How to use

*WinSCP is for Windows, this step by step explain how to use it on a Windows computer.*

1. Get a copy of the script: [download it](https://raw.githubusercontent.com/cavo789/winscp/master/synchronize/winscp_Synchronize.txt.txt) and save it, f.i., as  `c:\temp\ftp\winscp_Synchronize.txt.txt` (or update the script, see 2.1)
2. Edit the script and specify:
   1. in case of need, replace `ftp`by `sftp` (line 9)
   2. `USERNAME`: the FTP username (line 9)
   3. `PASSWORD`: the password associated to this account (line 9)
   4. `HOST_OR_IP`: the FTP host name or his IP (line 9)
   5. `PORT`: the port to use (21 for a FTP connection f.i., line 9)
   6. The local folder where your files are stored (line 23)
   7. The remote folder from where the files should be copied (line 24), can be the ftp root or any sub-folder
3. Start a DOS session (start -> run -> cmd.exe)
4. Go to your `c:\temp\ftp` folder: type `cd \temp\ftp`
5. Run `winscp.com` from there: type `"c:\program files (86)\WinSCP\WinSCP.com" /script="c:\temp\ftp\winscp_Synchronize.txt"`

If everything is correctly set up, WinSCP will start a session terminal and will start the synchronization.

#### More info

More info about the Synchronize verb of WinSCP: https://winscp.net/eng/docs/scriptcommand_synchronize

* If files/folders are already there, don't do anything.
* If there are new files/folders, copy them.
* If files/folders are no more on the local drive, remove them from the remote server.

So local is the master.

##### Local / Remote / Both

See the line 25 of the `.txt` script: synchronize remote.

Choose remote, local or both:

When the first parameter is local, changes from remote directory are applied to local directory. When the first parameter is remote, changes from the local directory are applied to the remote directory. When the first parameter is both, both local and remote directories can be modified ([source](https://winscp.net/eng/docs/scriptcommand_synchronize#remarks)).

### VBA - Visual Basic for Application

The `WinSCP` DLLs can be called up through a VBA code and therefore it would be possible to upload / download a file to / from an FTP server.

See [https://winscp.net/eng/docs/library_vb#using](https://winscp.net/eng/docs/library_vb#using). There are also several posts in the forum: [https://winscp.net/forum/search.php?mode=results](https://winscp.net/forum/search.php?mode=results)

```vbnet
Option Explicit

Sub Example()

    Dim mySession As New Session

    ' Enable custom error handling
    On Error Resume Next

    Upload mySession

    ' Query for errors
    If Err.Number <> 0 Then
        MsgBox "Error: " & Err.Description

        ' Clear the error
        Err.Clear
    End If

    ' Disconnect, clean up
    mySession.Dispose

    ' Restore default error handling
    On Error GoTo 0

End Sub

Private Sub Upload(ByRef mySession As Session)

    ' Setup session options
    Dim mySessionOptions As New SessionOptions
    With mySessionOptions
        .Protocol = Protocol_Sftp
        .HostName = "example.com"
        .UserName = "user"
        .Password = "mypassword"
        .SshHostKeyFingerprint = "ssh-rsa 2048 xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx"
    End With

    ' Connect
    mySession.Open mySessionOptions

    ' Upload files
    Dim myTransferOptions As New TransferOptions
    myTransferOptions.TransferMode = TransferMode_Binary

    Dim transferResult As TransferOperationResult
    Set transferResult = mySession.PutFiles("c:\temp\*", "/home/user/", False, myTransferOptions)

    ' Throw on any error
    transferResult.Check

    ' Display results
    Dim transfer As TransferEventArgs
    For Each transfer In transferResult.Transfers
        MsgBox "Upload of " & transfer.Filename & " succeeded"
    Next

End Sub
```

## License

[MIT](LICENSE)
