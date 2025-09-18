+++
date = "Mon, 08 Feb 2016 17:20:00 +0000"
title = "OSX Keychain Prompting for SSH key passphrase even after saving it"
slug = "osx-keychain-prompting-for-ssh-passphrase"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# OSX Keychain Prompting for SSH key passphrase even after saving it

From StackExchange: [http://apple.stackexchange.com/questions/62270/os-x-keychain-keeps-prompting-me-for-my-ssh-passphrase-even-though-i-save-it-os](http://apple.stackexchange.com/questions/62270/os-x-keychain-keeps-prompting-me-for-my-ssh-passphrase-even-though-i-save-it-os)  
and [http://apple.stackexchange.com/questions/18458/password-dialog-appears-when-ssh-private-key-permissions-are-set-to-0600/26252#26252](http://apple.stackexchange.com/questions/18458/password-dialog-appears-when-ssh-private-key-permissions-are-set-to-0600/26252#26252)  
  
The solution for getting a keychain prompt when using ssh and keys with your ssh config file using "identitiesonly yes":  

Make sure you have a corresponding `id_rsa.pub` or `id_dsa.pub` in your `~/.ssh` directory.

When I had an `id_rsa` but not a corresponding `id_rsa.pub`, Mac OS X kept popping up the dialog and remember passowrd in my keychain did nothing.

```
cd ~/.ssh
ssh-keygen -y -f id_rsa > id_rsa.pub
```

generated the appropriate public key file for me.

If you already had your public file there (rename it to another name) and generate the public key again using the above command, you'll notice that the generated and the old one are not equal. Somehow the older versions of Mac OS X generated a public key that Lion does not like anymore, generating it again fixes that.

For the curious, the key is exactly the same, the part that changes is that there is no "comments" section after the key on the file any longer.