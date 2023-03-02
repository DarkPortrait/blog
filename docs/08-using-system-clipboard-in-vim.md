If you are facing trouble in copying texts to and from VIM and another software, your VIM is not configured to access the system clipboard.

# Ubuntu 20.04

You need to have the +clipboard feature turned on to access the system clipboard from VIM.

In terminal, write `vim --version` and look for `+clipboard`

If it shows `-clipboard`, it means that the feature is not installed.

I did `sudo apt-get vim-gtk` to upgrade vim and ensured clipboard feature is added.

With this enabled, you should be able to use "+y to copy and "+p to paste. In my Ubuntu, Ctrl+Shift+V worked for pasting in Ubuntu.

But "+y is too many keys to press just for copying. I added the following in the .vimrc -
`set clipboard=unnamedplus`

This makes default y access the clipboard like the "+y combo.
