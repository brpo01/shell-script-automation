# **SHELL SCRIPTING**

In this project, we use a shell script to automate the creation of multiple users on a remote server. 

- We run this project using two virtual instances on aws(ec2). Let one of the servers act as a *client* and let the other act a a *remote* server

- On your client server, create a directory called Shell, and add three files namely:- names.csv, onboarding_users

```
client@ip-address~:$ mkdir Shell & touch names.csv onboarding_users
```

- When that is done create a shell script in your `onboarding_users` file

```
#!/bin/bash

#automate the creation of new users on your linux server.

CSV_FILE=names.csv

GROUP_NAME=developers

SSH_FOLDER=/etc/skel/.ssh

AUTH=authorized_keys

PASSWORD=password

#conditional for checking if the group exists

if [ $(getent group developers) ];
then
    echo group already exists
else
    sudo groupadd $GROUP_NAME
    echo new group developers created successfully
fi

#add .ssh folder to skel directory

if [ -d "$SSH_FOLDER" ];
then
    echo "$SSH_FOLDER" exists
else
    sudo mkdir -p $SSH_FOLDER
    sudo bash -c "cat $AUTH >> $SSH_FOLDER/authorized_keys"
fi

# create users on the server

while IFS= read USERNAME
    do
        if [ $(getent passwd $USERNAME) ];
        then
            echo $USERNAME already exists
        else
            sudo useradd -m -G $GROUP_NAME $USERNAME #Create the User & add to the developers group
            sudo echo -e "$PASSWORD\n$PASSWORD" | sudo passwd "$USERNAME" #SET USER PASSWORD
            sudo passwd -x 5 "{$USERNAME}" # USE passwd -x flag to set MAX_DAYS before password is changed
            sudo chmod 700 /home/$USERNAME/.ssh
            sudo chmod 644 /home/$USERNAME/.ssh/authorized_keys
            echo $USERNAME created successfully
        fi
    done < $CSV_FILE

exit 0
```
- Create a list of users in your `names.csv file`

```
dave
tems
dayo
roxanne
albert
billy
saunders
gary
rio
arnold 
shawn
bell
hendo
gundo
```

- Make your `onboarding_users` script executable by running:

```
client@ip-address~:$ chmod +x onboarding_users
```

- Run your Script using `./onboarding_users`

```
client@ip-address~:$ ./onboarding_users
new group developers created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
dave created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
temi created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
dayo created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
roxanne created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
albert created successfully
useradd: invalid user name 'joe '
passwd: user 'joe ' does not exist
passwd: user 'joe ' does not exist
chmod: cannot access '/home/joe /.ssh': No such file or directory
chmod: cannot access '/home/joe /.ssh/authorized_keys': No such file or directory
joe  created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
billy created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
saunders created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
gary created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
rio created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
arnold created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
shawn created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
bell created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
hendo created successfully
New password: Retype new password: passwd: password updated successfully
passwd: password expiry information changed.
gundo created successfully
```

- Before we can connect to the remote server, we have to update the sshd configuration in /etc/ssh/sshd and set `PasswordAuthentication` to yes on both servers

- Create a password for your logged in user using the `passwd` command

```
client@ip-address~:$ sudo passwd client
```

- Restart the sshd daemon service, so the changes can be updated. 

```
client@ip-address~:$ sudo systemctl restart sshd
```
- Use the `ssh-copy-id` command to copy the content of the keys of the client server unto the remote server

```
client@ip-address~:$ ssh-copy-id remote@ip-address
```

- When prompted for a password, input the password you created for your user. If all goes well, you should have copied the keys.

- You can connect to the remote server using the `ssh` command

```
client@ip-address~:$ ssh remote@ip-address
```

- Now that we have confirmed that the client server and remote server can connect to each other, we can transfer all of the files unto the remote server using the `scp` command. Secure copy protocol (SCP) is a means of securely transferring computer files between a local host and a remote host or between two remote hosts. 

```
client@ip-address~:$ scp -i /home/client/.ssh/authorized_keys remote@ip-address:

```
- All of the files should have been transferred to the the remote server. You can now run your onboarding_users script to create all the users on your remote server

```
remote@ip-address~:$ ./onboarding_users
```

This is how to use a shell script to automate the creation of multiple users on a remote server.

# **IMPLEMENTATION VIDEO**
- Link- https://drive.google.com/file/d/1DQDXO2KqMDJYpsCRX3YBqKiWeaWdwVxj/view?usp=sharing


