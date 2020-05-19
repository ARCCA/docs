Security highlights
===================

Why we take security seriously?
-------------------------------

HPC resources as well as many services you will use, are shared resources. They are used by many researchers studying a wide range of problems. Any issues arising from the actions of one individual can therefore impact many of your colleagues and result in resources being taken offline whilst issues are investigated and remedied. Furthermore they are expensive resources typically run by small or modest sized teams so impacts, whilst hopefully rare, can be costly in time and personpower. This is before we consider the potential loss of data or breaches of sensititve data.

  | Security, a shared responsibility
  | 
  | Users and system operators have a shared responsibility to ensure the security of resources. System operators monitor the system, ensure security patches are up-to-date and that the systems is configured to restrict access correctly e.g. so that users cannot see each others data/files by default.
  | 
  | Users should follow best practice to mitigate the risk of accounts and access details being compromised, e.g. we all know that we must keep our passwords secret. You shouldn't make your files accessible to others unless necessary. This episode specifically addresses how we can use ssh and associated tools to securely access remote resources and mitigate potential security issues.
  |
  |All of the approaches described here, to assist your secure use off ssh, also apply when transfering data using tools such as scp, rsync and sftp.

SSH Clients
-----------

As HPC services are remote machines, interaction is done over an encrypted communication channel called Secure Shell version 2 (SSH-2). This allows command-line access to one of the login nodes of a HPC service, from which you can run commands or use a command-line text editor to edit files. ssh can also be used to run graphical programs such as GUI text editors and debuggers when used in conjunction with an X server.
Logging in from Linux and macOS

Linux distributions and macOS each come an installed terminal application that can be used that can be use for SSH access to the login nodes. Linux users will have different terminals depending on their distribution and window manager (e.g. GNOME Terminal in GNOME, Konsole in KDE). Consult your Linux distribution’s documentation for details on how to load a terminal.

macOS users can use the Terminal application, located in the Utilities folder within the Applications folder.

You can use the following command from the terminal window to login into an HPC service:

.. code-block:: text
  ssh [userID]@<hpc-service>

To allow remote programs, especially graphical applications to control your local display, such as being able to open up a new GUI window (such as for a debugger), use:

.. code-block:: text
  ssh -X [userID]@<hpc-service>

Some sites recommend using the -Y flag. While this can fix some compatibility issues, the -X flag is more secure.

Current macOS systems do not have an X window system installed by default. Users should install the XQuartz package to allow for SSH with X11 forwarding on macOS systems:

    XQuartz website

Logging in from Windows
-----------------------

MobaXterm
^^^^^^^^^

A typical Windows installation will not include a terminal client, though there are various clients available. We recommend all our Windows users to download and install MobaXterm to access HPC facilities. It is very easy to use and includes an integrated X server with SSH client to run any graphical applications.

You can download MobaXterm Home Edition (Installer Edition) from the following link:

    Install MobaXterm

Double-click the downloaded Microsoft Installer file (.msi), and the Windows wizard will automatically guides you through the installation process. Note, you might need to have administrator rights to install on some Windows OS. Also make sure to check whether Windows Firewall hasn’t blocked any features of this program after installation.

Start MobaXterm using, for example, the icon added to the Start menu during the installation process.

If you would like to run any small remote GUI applications, then make sure to use -X option along with the ssh command (see above) to enable X11 forwarding, which allows you to run graphical clients on your local X server.
Putty

A common option found on many University systems is to use Putty/Kitty

    Download Putty

WSL
^^^

If you are using Windows Subsytem for Linux then you can install ssh as described above in Logging in from Linux and macOS.

Interactive access
------------------

To log into your HPC service you should replace the [userID] and address provided:

.. code-block:: text
  ssh [userID]@<hpc-service>

Initial passwords
^^^^^^^^^^^^^^^^^

On local, HPC systems you may be able to use your standard institutional credentials. On other services you will typically be provided with your initial password for logging onto the system.

When you log into a new service for the first time you will likely be asked to change your password. If you are not, you should change it at first log-in with the command passwd. Once you have logged in or run the command, the password change sequence is:

    Enter your current password:
    Enter a new strong password
    Re-enter the same new password.

There may be a forced password policy to help ensure that you are using a strong password.

N.B. You may be logged out and can now log back in with your new password.

You may now change your password on the machine itself using the passwd command. If you forget your password, you should contact your system administrator/helpdesk.

  | Strong Passwords
  | 
  | A strong password describes a password that is difficult to detect by both humans and computer programs, effectively protecting data from unauthorized access. A strong password consists of at least twelve characters (and the more characters, the stronger the password) that are a combination of letters, numbers and symbols (@, #, $, %, etc.) if allowed. Passwords are typically case-sensitive, so a strong password contains letters in both uppercase and lowercase. Strong passwords also do not contain words that can be found in a dictionary or parts of the user's own name.
  |
  | Based on https://www.webopedia.com/TERM/S/strong_password.html

  | What is a public key pair?
  |
  | A public key pair consists of two parts, a public part and a private part which are related. One is used to lock the message the other is used to unlock the message:
  | 
  |  * The public part is used to lock (encrypt) the message so that it can be sent over the internet and can be shared.
  |  * The private part is used to unlock (decrpyt) the message and should not be shared.

Set up an SSH key pair protected by a passphrase
------------------------------------------------

Some systems will also/instead require you to set up an SSH key pair to access the services. The SSH key pair consists of a private part and a public part. The public key can be put on remote machines to allow you to log-in without the use of a password. You keep the private part of the key secure on your local machine protected with a passphrase.

Public key encryption uses fancy maths to enable secure communication over an open channel. There are a number of methods the most common being RSA) which uses prime numbers. If you are going to use this you should use a key size of at least 2048 and preferably 4096. Public keys can be broken with brute force computation and the longer the key the more secure it is.

An alternative encryption method and the one we recommend you to use are based on EdDSA (Ed25519). For our purposes the key pair works in the same way. Your public key can go on the remote resource or service and the private key is kept protected on your local machine. You can generate a key pair with:

.. code-block:: text
  ssh-keygen -o -a 100 -t ed25519

    ``ssh-keygen`` is the command to generate the key pair
    ``-o`` specifies to use a strong format to save the key
    ``-a 100`` increases the strength of encryption with your passphrase
    ``-t ed25519`` specifies the encryption method used

When you create a SSH key pair you will be prompted to provide a passphrase. This is effectively password for your private key and like a password should be kept secret. Now when you try to use the key, you should be asked for your key pair passphase (which you entered when you created the key pair) rather than your remote machine password.
Use passphrases

If you do not use a passphrase then if someone gets hold of your private key they will be able to use your key to log in on any machine where you use that key.

On systems running older version of ssh you may not be able to use the EdDSA encryption. In this case you should use:

ssh-keygen -o -a 100 -t rsa -b 4096

Private keys are private

While it is necessary to share you public key in order to use public key encryption, the private key should never be shared or stored on remote services even though it is protected with a passphrase.
National and regional resources

On many national and regional resources e.g. ARCHER, ARCHER2 and the Tier-2 sites it is compulsory to use public keys and these will often be required as part of your application to use the service and added for you. For completeness we now describe how you can add you public key to the remote service:
Copy the public part of the key to the remote host

When you create the key pair two files will be generated, a private key e.g. id_ed25519 (or id_rsa) and the public key id_ed25519.pub (or id_rsa.pub). Your private key should never be copied to different machines, however, in order to use your key pair you do need to copy the public key to the remote machine.

Using you normal login password, add the public part of your key pair to the authorized_keys file on the remote host to which you wish to connect. We can use the utility ssh_copy_id to do this:

ssh_copy_id -i ~/.ssh/id_ed25519.pub [userID]@<hpc-service>

Now you can test that your key pair is working correctly by attempting to connect to the remote host and run a command. You should be asked for your key pair passphase (which you entered when you created the key pair) rather than your remote machine password.

ssh [userID]@<hpc-service> 'date'
Enter passphrase for key '/Home/user/.ssh/id_rsa': [Passphrase]
Wed May  8 10:36:48 BST 2020

We have run date on the remote server to confirm that we have been able to use the key pair, and passphrase to log in.
What is ssh-copy-id doing?

ssh-copy-id is appending the contents of the public part of the key to the remote file ~/.ssh/authorized_keys.

You could also copy and paste your public key into the remote ~/.ssh/authorized_keys but using the provided tool makes this easier.

If you do this make sure that you don't replace existing keys that you want to keep.
Using someone else's key

Should you share someone else's key?

Solution
Key permissions

ssh has strict requirements on the permissions for private keys. Why would this be?

Solution
Key pairs for multiple services

So far we have generated a single key with a default name for one service. Using strong keys means that the key should secure but what happens if the key is compromised? An intruder can now access all systems on which we use this key. Therefore it is good practice to use a different key for each service you use. In order to do this you need to specify the name of key file:

ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519_service

Before when we connected to the remote machine ssh automatically tried default keys it found in ~/.ssh. We can specify that we wish to use a specific key with:

ssh -i ~/.ssh/id_ed25519_service [userID]@<hpc-service>

However we now have an issue that we need to remember and specify the key we want to use for each service and typeout a longer command each time we want to connect to remote machines. We can simplify this by adding the Host, and key file to our ssh config. Edit ~/.ssh/config and add/include:

Host <hpc-service>
    IdentityFile ~/.ssh/id_ed25519_service

Now when we connect to the service:

ssh [userID]@<hpc-service>

More features of config

You can also use the ssh config to specify many more features of your connection e.g. if you have different usernames on different systems:

Host service
  IdentityFile id_ed25519_service
  User userid_service

Enabling the SSH Agent

So far we have just replaced the need to enter a password to access a remote host with the need to enter a key pair passphrase. Because of this is may be tempting to leave the passphrase empty when creating your key so that we do not have to enter it every time we access a service which may be many times a day. This is poor security practise and is likely to be in breach of the acceptable use policies covering the services you are accessing.

It is also a completely unnecessary risk as you can enable an SSH Agent on your local system so that you only have to enter the passphrase once and after that you will be able to access the remote system without entering the passphrase.
Start ssh-agent

Most modern Linux distributions (and macOS) should have ssh-agent running by default. If your system does not then you should find the instructions for enabling it in your distribution using Google. Typically you can check this with:

echo $SSH_AGENT_PID

If the output is empty then it isn't running. It can be launched with:

eval `ssh-agent
Agent pid 123

and now you can confirm it is running with:

echo $SSH_AGENT_PID
123

Agent lifetime

By default ssh-agent will store your key forever, which is typically the lifetime of the shell session. Remember that we are trying to ensure that we operate as securely as possible. If we could be completely confident that our local machine could not be compromised then we would not use passwords or passphrases.

Similarly with the ssh-agent we must consider how long that the passphrase needs to be or should be remembered. If we are in a secure office at work and we will be accessing the service repeatedly throughout the day then we might want the key to be remembered for several hours. If we are doing half an hour's work in a cafe we would probably want to have the keys stored for that length of time. Note that the time is in seconds.

At the end of a session you can remove all stored keys with:

ssh-add -D

Adding your key to the agent

To add the private part of your key pair to the SSH Agent, use the ssh-add command (on your local machine). To add the key for one hour we inculde the flag and parameter -t 3600, you will need to enter your passphrase one more time:

ssh-add -t 3600 ~/.ssh/id_ed25519-service
Enter passphrase for home/user/.ssh/id_ed25519_service: [Passphrase]
Identity added: home/user/.ssh/id_ed25519_service (home/user/.ssh/id_ed25519_service)
Lifetime set to 3600 seconds

Now you can test that you can access the remote host without needing to enter your passphrase:

ssh [userID]@<hpc-service> 'date'
Wed May  8 10:42:56 BST 2020

again we have run date on the remote service to confirm that we have been able to use the ssh-agent successfully.

Remember that in the above user will be your username on your local machine and that [userID] is you username on the remote <hpc-service>.
Moving data

If you use scp, rsync or sftp to transfer files then since these use ssh they will use your ssh config file and stored keys in exactly the same way as running ssh.
Remote key pairs

You should never store the private key on a shared resource. On some HPC services you may see that a key pair has been created automatically in you remote .ssh folder. This is used by some schedulers to manage communication and should only be used by the system for internal communcations e.g. you logging onto nodes running jobs or interactive sessions and parallel communcations within jobs.

These keys are not protected by passphrases and should never be used for onward connections from the remote service.
Key Points:

    breaches of security can result in loss of services, lost data or breaches of sensitive data
    security of HPC services is a shared responsibilty
    ssh is a secure protocol for accessing remotes services and transfering data
    the use of ssh keys restrict access to users with public keys registered on the remote service
    passphrases must be used to protect private keys
    Your private keys should never be stored on remote services
    Appropriate use of ssh config and the ssh-agent can simplify our secure use of these services
    ssh key pairs may be generated on HPC services for communicating on the resource. These keys are not protected by passphrases and should never be used for onward connections from the remote service.

