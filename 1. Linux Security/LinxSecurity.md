# **Linx Security Basics**

### I. **General System Tasks**
#### 1. Retrieve available updates
    sudo apt update



#### 2. Upgrade your system
    sudo apt upgrade -y



#### 3. Reboot your system
    sudo reboot


---
### **II. User Tasks**
#### 1. Change the current user to root
    sudo su root



#### 2. What is the difference between the two newly created users?
    useradd (for bob) || adduser (for sally)




#### 3. Change the current user to sally



#### 4. Attempt to create a new user as sally
- what happens, why



#### 5. Delete bobby the user


#### 6. Changee the password of sally
    sudo passwd sally


#### 7. Why is it bad practice to stay logged in as root?

#### 8. Check what the user id is:
    id 




---
### **III. Group Tasks**
#### 1. What groups does ubuntu belong to? 



#### 2. Give sally the ability to execute sudo commands. Create a new user while logged in as sally. 



#### 3. Create a new group called "cybersec"



#### 4. Add sally to cybersec



#### 5. How many ways are thrr to check which group sally belongs to?




---
### **IV. Permission and Accss Control Lists**
#### 1. Create a new directoy called "lab1" and check the permissions
Who is th owner and the goup owner of the directory?
What permissions dos the owner, group and other have?



#### 2. In the new dirctory, create a file to print out "Hello World!"



#### 3. What are th reading, writing, and executing permissions for th owner, group and other?
    Is -I helloWorld

Chang th permissions so the group also has w and x permissions



#### 4. View the ACL of the file
    getfacl



#### 5. Allow sally to read and write the file
    stfacl






---
### **V. Section Header**
#### 1. Focus



#### 2. Focus



#### 3. Focus



#### 4. Focus



#### 5. Focus




---
### **VI. Section Header**
#### 1. Focus



#### 2. Focus



#### 3. Focus



#### 4. Focus



#### 5. Focus




---
### **VII. Section Header**
#### 1. Focus



#### 2. Focus



#### 3. Focus



#### 4. Focus



#### 5. Focus





---
### **VIII. Section Header**
#### 1. Focus



#### 2. Focus



#### 3. Focus



#### 4. Focus



#### 5. Focus


