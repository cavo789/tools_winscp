# VBA - Visual Basic for Application

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
