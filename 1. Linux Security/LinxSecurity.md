# **Linx Security Basics**

### I. **General System Tasks**
#### 1. Retrieve available updates
    sudo apt update

![Retrieve Available Updates](<Retrieve Available Updates.png>)

#### 2. Upgrade your system
    sudo apt upgrade -y

![Upgrade System Command](<Upgrade System Command.png>)
sudo 
#### 3. Reboot your system
    sudo reboot

---
### **II. User Tasks**
#### 1. Change the current user to root
    sudo su root

![Change user to root](<Change user to root.png>)

#### 2. Create a new user with the name "bobby" using "useradd," and a new user with the name "sally" using "adduser" What is the difference between the two newly created users?
    useradd (for bob) || adduser (for sally)

![Creating new usrs bobby and sally](<Creating new users (bobby and sally).png>)

useradd: directly adds a new user to th password file (doesn't create a home directory or password for the new user)

adduser: a perl script that walks though the steps of creating a password and home directory for the new user


#### 3. Change the current user to sally
    sudo su sally   

![Change user to sally](<Change user to sally.png>)

#### 4. Attempt to create a new user as sally

An error message pops up stating that sally doesn't have permission to do this because regular users don't have root privilegs.

Sally would need admin level privilegs in order to add new users She would need to be in the sudo group

![sally trying to create earl](<sally trying to create earl.png>)

#### 5. Delete bobby the user
    sudo deluser bobby
![deleting bobby](<deleting bobby.png>)

#### 6. Changee the password of sally
    sudo passwd sally

![change sallys password](<change sallys password.png>)

#### 7. Why is it bad practice to stay logged in as root?
It's bad practice because it increases the risk of making accidental system changes that could destroy system files, potentially giving an attacker complete access if the system is compromised

#### 8. Check what the user id is:
    id 

![check user id](<check user id.png>)


---
### **III. Group Tasks**
#### 1. What groups does ubuntu belong to? 
    groups ubuntu

![ubuntu groups](<ubuntu groups.png>)

#### 2. Give sally the ability to execute sudo commands. Create a new user while logged in as sally. 
    sudo usermod -g sudo sally
![put sally in sudo](<put sally in sudo.png>) 
![sally creates earl](<sally creates earl.png>)

#### 3. Create a new group called "cybersec"
    sudo groupadd cybersec 
![create cybersec group](<create cybersec group.png>)

#### 4. Add sally to cybersec
    sudo usermod -aG cybersec sally
![add sally to cybersec](<add sally to cybersec.png>) 

#### 5. How many ways are there to check which group sally belongs to?
    2 ways: groups sally || id sally

![check sallys groups](<chck sallys groups.png>)


---
### **IV. Permission and Accss Control Lists**
#### 1. Create a new directoy called "lab1" and check the permissions

![create lab1 directory](<create lab1 directory.png>)
![check lab1 perms](<check lab1 perms.png>) 

Who is the owner and the goup owner of the directory?
- The owner is whoever created it (root) and the group owner is also root

What permissions dos the owner, group and other have?
- The owner has have read, write, and execute privileges while others would have read and execute privileges


#### 2. In the new dirctory, create a file to print out "Hello World!"

![print helloWorld](<print helloWorld.png>)

#### 3. What are the reading, writing, and executing permissions for the owner, group and other?
    Is -I helloWorld

![helloWorld permission](<helloWorld permissions.png>)

Owner permissions: read, write, execute

Group permissions: read, execute

Other permissions: read, execute

#### 4. View the ACL of the file
    getfacl helloWorld

![get helloWorld facl](<get helloWorld facl.png>)

#### 5. Allow sally to read and write the file
    setfacl -m u:sally:rw helloWorld

![give sally perms in helloWorld](<give sally perms in helloWorl.png>)