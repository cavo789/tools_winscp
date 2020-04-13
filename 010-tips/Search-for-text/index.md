# Search for files that contain a keyword

It is possible to launch a search to, for example, find the string *Notes management* in all the `.php` files of the remote site.

To do this, go to the `Commands` menu, then `Static custom commands` and finally `Search for Text...`.

It is, in fact, the execution of a script (an extension in the WinSCP language) which is here : `C:\Program Files (x86)\WinSCP\Extensions\SearchText.WinSCPextension.ps1`

![Search for Text...](./images/search_for_text_1.png)

The search result is displayed in a Powershell console:

![Console output](./images/search_for_text_2.png)
