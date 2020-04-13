# Download files with specific extension recursively

> How to quickly get all php scripts under /images? This script given the answer

The WinSCP FTP client ([website](https://winscp.net/eng/index.php)) allow to run a script that make possible automation.

The idea under the script below is to connect to a FTP server, go to the ftp root folder and then, recursively, get all files having the php extensions.

I've coded this small script when facing the following problem: there were a lot of viruses in the /images folder of a website and that specific folder has hundreds of sub-folders and the `totalsize` of `/images` was bigger than 1 GB.

## The script

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

## How to use

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
