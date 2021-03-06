# Linux Security

## Intro to Linux Security
- If a bad guy gets access to your server, they can do anything they want with it. 
   - ex. Send spam, launch denial of service attacks, and so much more.
- we'll discuss a number of security-related topics
   - managing users 
   - packages or the software installed on the server
   - various methods of authenticating users
   - how Linux manages file permissions
   - how to configure a firewall.

## The Rule of Least Privilege
- Definition: a user or an application only has enough permission to do its job, nothing extra. 

## Becoming a Super User

- every Linux machine comes with the user name root 
- root user can do anything they want on this machine. 
- It's very common to disable the ability to remotely log in as root.
- Instead, we'll log in as a user we create, and then we can run individual commands as root by using another command. 
- This is to make any potential attacker's job a little more difficult by eliminating the username that they already know exists on this on this box. 
- it's highly advised that this be one of the very first things you do when you're setting up a new server.

## sudo vs su

- It's typically regarded as a best practice that you not use the `su` command.
- if you forget that you are currently within SU, You could potentially do some extremely damaging operations, and there's no safety net there to warn you when doing so.
- not every user has the ability to work as the superuser. You have to give that user those permissions specifically. 

## Package Source Lists

- A list of various places we can go to get software.
- All of your available package sources are listed in this file `/etc/apt/sources.list`
- Each distribution approves and releases packages in their own way and that's one of the big ways in which they differ.

## Updating Available Package Lists

- One of the most important and simplest ways to ensure your system is secure is to keep your software up to date with new releases. 
- Linux systems focus on reliability and they run a variety of complex applications that have numerous dependencies of their own, most Linux distributions do not auto-update the software installed on the system.
- You'll need to do this yourself and test your apps to make sure any recent updates don't break your application.
- The first step to upgrading your installed software is to update your package source list.
- We do this with the command `sudo apt-get update`. The update command will run through all of the repositories we saw within our `etc/apt/sources.list` file, and it will check to see what all software is available and what those version numbers are.
- This command doesn't actually perform any changes to the software on your system.
- It just makes sure your system is aware of the latest information stored within all of these repositories that you're making use of.

## Upgrading Installed Packages

- our system is aware of what all software is available, and the most recent version numbers, 
- `sudo apt-get upgrade`.
- you'll be presented with a list of everything that's going to change on your system.
- when setting up a new machine, you can be pretty safe in just accepting that the system is always making the best decisions for you.
- When running your web application, you want to take more care in reviewing this list.
- test everything in a non-production environment before performing similar operations on your production server.

## Other Package Related Tasks

- The apt get application is your main interface to a ton of package related functionality.
- We can check out everything you can do by reading the man page for it with this command, `man apt-get`.
- If there are some packages that are no longer required that we can just automatically remove, `apt-get autoremove`.
- We can install an application called finger by typing the command `sudo apt-get install finger`, 

## Discovering Packages

- Fortunately, each distribution tends to publish an easy to browse version of their repositories.
- For Ubuntu, you can search for packages at packages.ubuntu.com.

## Using Package Search
Package Name
- Apache HTTP Server : apache2
- PostgreSQL : postgresql
- Memchache : memcached

## Using Finger

- Finger will look up various pieces of information about a user, and display it in an easy to read format.
- Finger command will output information about all of the users currently logged into the system.
- You can also pass a username to the Finger application to see additional information about a specific user.
- Type finger vagrant, and you'll see some additional information including our home directory and what shell we're using.

## Introduction to /etc/passwd

- finger retrieves all of this information such as our user name, home directories, the shell from `etc/passwd`.
- Each line within this file is an entry for a single user
- each entry has a number of fields that are separated by colon characters.
The line looks like this,
vagrant: x:1000:1000: :/home/vagrant:/bin/bash.

**/etc/passwd**

This is a very important file on your system! It's used to keep track of all users on the system. Run cat /etc/passwd and look at the output; each line is organized in this format:

**username:password:UID:GID:UID info:home directory:command/shell**

Let’s run through what each of those mean:

1. username: the user’s login name
1. password: the password, will simply be an x if it’s encrypted
1. user ID (UID): the user’s ID number in the system. 0 is root, 1-99 are for predefined users, and 100-999 are for other system accounts
1. group ID (GID): Primary group ID, stored in /etc/group.
1. user ID info: Metadata about the user; phone, email, name, etc.
1. home directory: Where the user is sent upon login. Generally /home/
1. command/shell: The absolute path of a command or shell (usually /bin/bash). Does not have to be a shell though!

## User Account Info

- Using a combination of the finger application and reading the etc/password file, identify the following information for the root user.
- User ID and the Group ID are both 0.
- The root user will always have 0 as its User and Group ID.
- The Home Directory is /root.
- And the default shell is /bin/bash.

## Introduction to User Management

- it's a common pattern to disable the ability to log in as root and only log in as a different user that has the ability to use Sudo.
- This is a security measure, since every bad guy out there knows every Linux box has a user named root.
- By disabling this account from remote log, in we remove a very easy attack vector.

## Creating a New User

- We can create a new user by using the adduser command. `sudo adduser`
- create a new user named student. `sudo adduser student`
-  We'll then be asked to enter a password for the student.
- We're then asked the number of additional questions about the user.
- All of these are optional, so you can ignore them.
- We can confirm this user was created by using the finger command.

## Connecting as the New User

- connect to the server using this command, `ssh student@127.0.0.1 -p 2222`.
- We've been connecting to our server using vagrant SSH
- `SSH` is the application we use to remotely connect to the server.
- `127.0.0.1` is the IP address we want to connect to. 
- `127.0.0.1` is a standard IP address that always means localhost
- `student` is the user we want to log in as.
- `-p 2222` flag tells us to connect using port 2222.
- After hitting Enter, you may be asked an authenticity verification question.
- Just hit yes, and then you're asked for the user's password.

## Introduction to etc sudoers
- `sudo cat /etc/passwd`.
- We're asked for a [sudo] password for the student which is just the standard password we set for the user when we initially made the user.
- We now get this warning. "The student is not in the sudoers file. This incident will be reported".
- Our students does not have permission to use the sudo command.
- switch back to a user we know can run sudo commands.
- the list of users that are allowed to do this is within the etc/sudoers file.
- Let's read that file using `sudo cat /etc/sudoers`.
   - Here we can see that the root user is listed along with a few groups using % and then the name of the group.
- On some systems you would just add the student user just like this, using a special program called `visudo`, that's allowed to edit this file.
- on Ubuntu, it handles it a bit differently.
- If you look at the very bottom of this file,
there's a here that says `includedir /etc/sudoers.d`.
- This command tells the system to also look through any files in etc/sudoers.d and include those as if they were written directly within this file.
- This is a common pattern since distribution updates could overwrite this file.
   - And if that were to happen, you would lose all of the users you added.
   - By keeping your customizations in this other directory, the system eliminates that risk.
   - Let's see what files are currently included in that directory by running sudo ls etc/sudoers.d.

## Giving Sudo Access

- give our student user access to sudo themselves.
- I'll first copy the vagrant file and name it student.
   - `sudo cp /etc/sudoers.d/vagrant /etc/sudoers.d/student`   
- I'll then need to make a small edit to this file and
   - `sudo nano /etc/sudoers.d/student`
   - The file name doesn't mean anything,
   - change the word vagrant here to student.
-.Our student user now has access to use sudo.

## Resetting Passwords

- The user themselves could reset their password using the passwd command, but you can't rely on that user to do so all the time.
- As a super user, you can foresee users password to expire.
- Enter the command that you would use to force the student user's password to expire.
   - `sudo passwd -e student`
   - it force the student user to reset their password the next time they login.
   - Student is the user and this -e sets that password to expire.

## Another Authentication Method

- Hopefully, you chose a strong password since attackers will soon start running bots against your server attempting to guess any valid usernames and passwords.
- There's another way to perform user authentication that's much more secure.
- It doesn't rely on passwords, which we're pretty horrible at making secure, since we have to make them simple enough to memorize.
- key based authentication, relies on physical files located on the server and your personal machine, the one you're logging in from.

## Public Key Encryption

- Let's imagine I wanted to send a message to Cameron without anyone else being to see that message.
- If I were to just place this note on Cameron's desk, anyone could come by and read it.
- I could find a box with a lock and lock the message away but then I have to somehow get this key to Cameron.
- That's not going to work because anyone will be able to come by and grab the key.
- But what if Cameron already had a box set up on his desk, with his own lock, and the key to unlock that box is always in Cameron's pocket?
- He never shares that key with anyone.
- I can then come by his desk, place my message in the box, and lock it. And no one else can ever see that message.
- In this example, the box is called the public key. The box can be left out in the open, shared around without any consequence.
- The key in Cameron's pocket is called a private key. Cameron never lets anybody else borrow that key or see it.
- This combination of public and private keys allows me to securely communicate with Cameron.
- This same cryptography trick can be used to authenticate a client with a server.
- The server will send a random message to the client. 
- The client will encrypt that message with their private key, and then send that encrypted message back to the server.
- The server will decrypt this message with their public key
- if that value equals the same value they sent
- everything checks out and the client has authenticated.

## Generating Key Pairs

- We'll generate our key pair on our local machine, not on our server.
   - Remember, you never, ever want to share your private key with anyone else.
   - It should remain firmly in your possession at all times.
   - For this reason, you always generate key pairs locally.
- If you were to generate the key pair on the server, you cannot claim that the private key has always been private.
- We'll generate our key pair using an application called `ssh-keygen`.
   - You will first be asked to give a file name for the key pair.
   - I've given this one the name `Users/Udacity/.ssh/linuxCourse`.
   - This directory here is the default directory that key pairs should exist
- We'll then add a pass phrase to our key pair, just in case someone does happen to get these files.
- This passphrase will prevent them from actually using them.
- you'll see that ssh-keygen has generated two files, `linuxCourse` and `linuxCourse.pub`.
- `linuxCourse.pub` is what we'll place on our server to enable key based authentication.

## Supported Key Types

- The ssh-keygen application can generate key pairs of various types.
- The key you just generated was an RSA key, which is the default type if you don't specifically define a different type.
- The SSH version two protocol supports the DSA, ECDSA, ED25519, and RSA key types.
- MD5 and SHA256 are hashing algorithms that are not suitable for public key encryption.

## Installing a Public Key

- we've generated our key pair locally, we still need to place the public key on our remote server, so SSH can use it to log in.
- There are multiple ways to do this and there are even some applications that will do most of the work for you, but we're going to do it the manual way.
- First we want to make sure we're logged into our server as the student.
- `mkdir .ssh` command within my home directory.
- This is a special directory where all of your key related files must be stored.
- create a new file within this directory called authorized keys. `touch .ssh/authorized_keys`
- This is another special file that will store all of the public keys that this account is allowed to use for authentication, with one key per line in that file.
- Back on my local machine I've read out the contents of `linuxcourse.pub`, copy that.
- Then, back on my server as the student user,
- edit this authorized key file. `nano .ssh/authorized_keys` just paste in that content and save it.
- The final thing we need to do is set up some specific file permissions on the authorized key file and the SSH directory.
- This is a security measure that SSH enforces to ensure other users cannot gain access to your account.
- run `chmod 700 .ssh` on our SSH directory,
- run `chmod 644 .ssh/authorized_keys` on the authorized keys file.
- Finally we're all done and we can now log in as the student user
   - `ssh student@127.0.0.1 -p 2222 -i ~/.ssh/linuxCourse`
   - -i flag and the definition here of what key pair we want to use will allow me to login using that key pair.
   - If you set a passphrase for your key pair, you'll be asked to enter that.
- once you're done, you'll see you've logged into the server and you did not have to enter your remote password for this user.

## Forcing Key Based Authentication

- The final thing you'll want to do to secure the authentication process is to disable the password base logins.
- This will force all of your users to only be able to login using a key pair.
- To do this, you'll have to edit the configuration file for SSHD.
   - `sudo nano /etc/ssh/sshd_config`
   - change `PasswordAuthentication yes` to `PasswordAuthentication no`
- Now, the SSHD service is currently running, and it only reads its configuration file when it's initially started up.
- So we need to restart the service so it runs with the new configuration option we just made.
- We restart the service by `sudo service ssh restart`
- Now all users will be forced to log in using a key pair. SSH will not allow users to log in with a user name and password any longer.

## Introduction to File Permissions

Let's dive a bit deeper into how Linux manages file permissions.
I've listed the contents of the students' home directory.
And we're going to look at three pieces of information provided here.
The first column we've looked at briefly before.
Remember the d means that this is a directory.
The dash means that it's a file.
Now, you might think that there are nine additional spaces here for information,
but what's actually happening is there are three separate sections and
each of those sections has three pieces of information.
Let's break out this entry here for bash.rc.
The three individual entries are rw-, r--, and r--.
Let's go ahead and label each of these entries.
This first one I'm labeling owner, then group, and finally everyone.
This means the owner can read and write the file.
The dash here indicates that the owner cannot execute the file.
If they could, there would be an x here.
Users within the group can read this file, but they cannot write or
execute it.
Finally, everyone can read this file, but they cannot write or execute it.

## Owners and Groups

- how do we identify who the owner in the group are?
- If we look back at our directory listing,
we'll see two columns, here in the middle.
Most of the entries read student student, but
there's one here that reads root root, and I'll come back to that one.
These two columns identify the owner and the group for each entry in this list.
Now it's important to remember, although each of these has the same word,
student, listed in the two columns,
they're in two entirely different things.
The system has a username student, which is the owner of the file.
And a group name student,
which was automatically created when we made this user.
This is pre-common practice on a Linux system,
to have a group name the same as the user.
Just remember that they are two entirely different things.
So, what's up with this entry here that has root root?
We can see that the entry's name is .., and that's just a shortcut for
the parent directory.
And we know we're in our student's home directory.
So this entry here, is the equivalent of /home.
That directory is owned by the root user, and has a group of root.
And if we look at the permissions,
we see that only the root user can write to that directory.
Lets test this out for ourselves.
I'll move into the directory using cd..
And then I'll try to write a file.
You'll get a permission denied error,
just as this permission system told us we would.
Only root is allowed to write in this directory.
And our current user's definitely not root.
We can still list the files in this directory, because we have read access,
and we even entered the directory because we have execute.
We just can't write.

## Identifying Owners and Groups

Explore the file system a bit and identify the owner and
group for each of these files or directories listed here.

All of these files are owned by the user, root.
But some of them have different group owners.
.bashrc has a group owner of root, /var/local has a group of staff,
/var/log has a group syslog, and this file has a group of root.

## Octal Permissions

- r = 4
- w = 2
- x = 1
- no permission = 0
- By adding the numbers together, we end up with a result identifying the full set of permissions to apply.

## chgrp and chown

- what if you need to change a files group or owner?
- `chown` : change owner
- `chgrp` : change group
- If we change the file's group to root using this command `sudo chgrp root` and then name of the file.
- If we try to cat this file again, you'll see that we can still read it.
- The group has no permissions on this file, so there's pretty much no effect.
- Our ability to read this file right now is determined by the owner setting, not the group setting.
- if we change the owner to root of the bash history file, `sudo chown root .bash_history`
- you'll see that we can no longer read the file.
- Permission is denied, and this is because only the owner can read and write the file and that owner's root.
- Our current user student would fall in the everyone category and they have no permission at all to read this file.


### Errata

The `.bash_history` file is created by the `bash` shell the first time you log out of your Linux system. This means that if you are logging into the system for the first time, it won't have been created yet. If you log out and log back in (e.g. `vagrant ssh` if you're using Vagrant here) then the file will be created.

You can also experiment with `chown` and `chgrp` using another file. To create an empty file named `testfile`, use the command `touch testfile`.

## Intro to Ports

- how does your server know which application is in charge of handling each type of request?
- Each of your applications are configured to respond to requests destined for a specific port.
- For example, a web server would by default respond on port 80, the default port for HTTP.
- We can control which ports our server is allowed to accept requests for using an application called the firewall.

## Default Ports for Popular Services

- Below are a few of the most common services a web server would run.
- HTTP runs on port 80.
- HTTPS is 443.
- SSH is 22.
- FTP is 21.
- POP3 is 110.
- And SMTP is 25.

## Intro to Firewalls

- We should only listen on the ports required for our application to function correctly.
- We can configure which ports we want our server to listen to using an application called a firewall.

## Intro to UFW

- Ubuntu comes with a firewall pre-installed called ufw, but it's not currently active.
- You can verify this by typing command, `sudo ufw status`. Status: inactive.
- start adding some rules to our firewall, then we'll actually turn it on.
- we initially block all incoming requests. `sudo ufw default deny incoming`
   - This is a good practice, as it makes it much easier to manage your rules.
   - Just block everything coming in, then only allow what you need.
- We can also establish a default rule for our outgoing connections, any request our server is trying to send out to the internet.
   - `sudo ufw default allow outgoing`
- check the status of your firewall by typing, `sudo ufw status`.
   - You'll see that it's currently still inactive, which is a good thing. We're just configuring our firewall now.
- We actually have to turn it on ourselves once we have everything how we want.
- If we were to turn the firewall on now, we'd we blocking all incoming connections including SSH, which means our server would be dead in the water and completely inaccessible to us.


## Configuring Ports in UFW

- Let's start allowing the ports we know we'll need for the applications our server will be supporting.
- we'll need to support SSH so we can continue administering this server.
- Normally you would do this by typing sudo ufw allow ssh and you can go ahead and do that now.
- we're using a Vagrant virtual machine and
   - Vagrant set up our SSH on Port 2222.
   - So we'll need to allow all TCP connections through Port 2222 for SSH to actually work in our scenario here.
- For now, the only other application we plan to support is a basic HTTP server, 
   - so we can allow this by using `sudo ufw allow www`.
- we can now enable our firewall with `sudo ufw enable`.
- This step here can be a little hair raising, because our SSH connection is reliant upon these rules being correct.
If all of a sudden you lose your SSH connection to your server,
it's a pretty clear indicator that you messed up some of your rules.
Some cloud providers do offer a way to regain access to your system
through an external control panel.
But many others, you're just out of luck at this point.
- I recommend configuring your firewall pretty early in the server setup process.
- Finally, we can confirm all of our rules are set up as we indicated by using the `sudo ufw status` command.

## Conclusion

- There are a lot of different types of servers, email servers, chat servers, web application servers.
- Here are a few tutorials that will walk you through how to configure your server in a variety of use cases:
- ["LAMP" Stack (Linux, Apache, MySQL, PHP)](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-14-04)
- ["LEMP" Stack (Linux, nginx, MySQL, PHP)](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-14-04)
- [PEPS Mail and File Storage](https://www.digitalocean.com/community/tutorials/how-to-run-your-own-mail-server-and-file-storage-with-peps-on-ubuntu-14-04)
- [Mail-in-a-Box Email Server](https://www.digitalocean.com/community/tutorials/how-to-run-your-own-mail-server-with-mail-in-a-box-on-ubuntu-14-04)
- [Lita IRC Chat Bot](https://www.digitalocean.com/community/tutorials/how-to-install-the-lita-chat-bot-for-irc-on-ubuntu-14-04)
