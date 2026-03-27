# **Seed Security Lab**
[Set-up files](https://seedsecuritylabs.org/Labs_20.04/Software/Environment_Variable_and_SetUID/)

    Command: command here

**command**: what the command dones
- bullet point 1
- bullet point 2

`-highlighted`: specific highlight

*insert photo here*

### Task 1: Manipulating Environment Variables
#### Step 1. Use *printenv* or *env* to print out environment vairables
If you are interested in some particular environment variables, such as PWD, you can use "printenv PWD" or "env | grep PWD"


#### Step 2. Use *export* and *unset* to set or unset environment variables




### Task 2: Passing Environment Variables from Parent Process to Child Process
#### Step 1. Compile and run the following program, and describe your obsercation
The program can be found in the Labsetup folder; it can be compiled using "gcc myprintenv.c", which will generate a binary called a.out. Let’s run it and save the output into a file using "a.out > file"

    #include <unistd.h>
    #include <stdio.h>
    #include <stdlib.h>
    extern char **environ;
    void printenv()
    {
        int i = 0;
        while (environ[i] != NULL) {
            printf("%s\n", environ[i]);
            i++;
        }
    }
    void main()
    {
        pid_t childPid;
        switch(childPid = fork()) {
            case 0: /* child process */
                printenv(); ➀
                exit(0);
            default: /* parent process */
                //printenv(); ➁
            exit(0);
        }
    }



#### Step 2. Comment out the *printenv()* statement in the child process case (Line ➀), and uncomment the *printenv()* statement in the parent process case (Line ➁). 
Compile and run the code again, and describe your observation. Save the output in another file


#### Step 3. Compare the difference of the two files using the *diff* command. Please draw your conclusion


### Task 3: Environment Variables and *execve()*
#### Step 1. Compile and run the following program, and describe your observation. 
This program executes a program called /usr/bin/env, which prints out the environment variables of the current process.

Listing 2: myenv.c

    #include <unistd.h>
    extern char **environ;
    int main()
    {
        char *argv[2];

        argv[0] = "/usr/bin/env";
        argv[1] = NULL;
        execve("/usr/bin/env", argv, NULL); ➀

        return 0 ;
    }


#### Step 2. Change the invocation of *execve()* in Line ➀ to the following; describe your observation

    execve("/usr/bin/env", argv, environ);



#### Step 3. Please draw your conclusion regarding how the new program gets its environment variables

### Task 4: Environment Variables and *system()*
#### Step 1. using system(), the environment variables of the calling process is passed to the new program /bin/sh.
Please compile and run the following program to verify this.

    #include <stdio.h>
    #include <stdlib.h>
    int main()
    {
        system("/usr/bin/env");
        return 0 ;
    }



### Task 5: Environment Variable and Set-UID Programs
#### Step 1. Write the following program that can print out all the environment variables in the current process.

    #include <stdio.h>
    #include <stdlib.h>
    extern char **environ;
    int main()
    {
        int i = 0;
        while (environ[i] != NULL) {
            printf("%s\n", environ[i]);
            i++;
        }
    }


#### Step 2. Compile the above program, change its ownership to root, and make it a Set-UID program.

    // Asssume the program’s name is foo
    $ sudo chown root foo
    $ sudo chmod 4755 foo


#### Step 3. In your shell, use the export command to set the following environment variables (they may have already exist):
(you need to be in a normal user account, not the root account)

• PATH
• LD LIBRARY PATH
• ANY NAME (this is an environment variable defined by you, so pick whatever name you want).


### Task 6: The PATH Environment Variable and Set-UID Programs
Change the PATH environment variable in the following way (this example adds the directory /home/seed to the beginning of the PATH environment variable):

$ export PATH=/home/seed:$PATH

The Set-UID program below is supposed to execute the /bin/ls command; however, the programmer only uses the relative path for the ls command, rather than the absolute path:

    int main()
    {
        system("ls");
        return 0;
    }

Please compile the above program, change its owner to root, and make it a Set-UID program. Can you get this Set-UID program to run your own malicious code, instead of /bin/ls? If you can, is your malicious code running with the root privilege? Describe and explain your observations.


### Task 7: The *LD_PRELOAD* Environment Variable and Set-UID Programs
#### Step 1. First, we will see how these environment variables influence the behavior of dynamic loader/linker when running a normal program. Please follow these steps:
Let us build a dynamic link library. Create the following program, and name it mylib.c. It basically overrides the sleep() function in libc:


    #include <stdio.h>
    void sleep (int s)
    {
        /* If this is invoked by a privileged program, you can do damages here! */
        printf("I am not sleeping!\n");
    }

We can compile the above program using the following commands (in the -lc argument, the second character is `):

    $ gcc -fPIC -g -c mylib.c
    $ gcc -shared -o libmylib.so.1.0.1 mylib.o -lc

Now, set the LD PRELOAD environment variable

    $ export LD_PRELOAD=./libmylib.so.1.0.1

Finally, compile the following program myprog, and in the same directory as the above dynamic link library libmylib.so.1.0.1:

    /* myprog.c */
    #include <unistd.h>
    int main()
    {
        sleep(1);
        return 0;
    }


#### Step 2. After you have done the above, please run myprog under the following conditions, and observe what happens.
Make myprog a regular program, and run it as a normal user.

Make myprog a Set-UID root program, and run it as a normal user.

Make myprog a Set-UID root program, export the LD PRELOAD environment variable again in
the root account and run it.

Make myprog a Set-UID user1 program (i.e., the owner is user1, which is another user account), export the LD PRELOAD environment variable again in a different user’s account (not-root user) and run it.
#### Step 3. Please design an experiment to figure out the main causes, and explain why the behaviors in Step 2 are different. 
 (Hint: the child process may not inherit the LD * environment variables).

### Task 8: Invoking External Programs Using *system()* versus *execve()*
Listing 3: catall.c

    int main(int argc, char *argv[])
    {
        char *v[3];
        char *command;

        if(argc < 2) {
            printf("Please type a file name.\n");
            return 1;
        }

        v[0] = "/bin/cat"; v[1] = argv[1]; v[2] = NULL;
        command = malloc(strlen(v[0]) + strlen(v[1]) + 2);
        sprintf(command, "%s %s", v[0], v[1]);
        
        // Use only one of the followings.
        system(command);
        // execve(v[0], v, NULL);
        return 0 ;
    }

#### Step 1. Compile the above program, make it a root-owned Set-UID program. The program will use system() to invoke the command. If you were Bob, can you compromise the integrity of the system? For example, can you remove a file that is not writable to you?
Answer:

#### Step 2. Comment out the system(command) statement, and uncomment the execve() statement; the program will use execve() to invoke the command. Compile the program, and make it a root-owned Set-UID. Do your attacks in Step 1 still work? Please describe and explain your observations
Answer: 

### Task 9: Capacity Leaking
Compile the following program, change its owner to root, and make it a Set-UID program. Run the program as a normal user. Can you exploit the capability leaking vulnerability in this program? The goal is to write to the /etc/zzz file as a normal user.

Answer:

Listing 4: cap leak.c


    void main()
    {
        int fd;
        char *v[2];

        /* Assume that /etc/zzz is an important system file,
        * and it is owned by root with permission 0644.
        * Before running this program, you should create
        * the file /etc/zzz first. */
        fd = open("/etc/zzz", O_RDWR | O_APPEND);
        if (fd == -1) {
        printf("Cannot open /etc/zzz\n");
        exit(0);
        }

        // Print out the file descriptor value
        printf("fd is %d\n", fd);
        // Permanently disable the privilege by making the
        // effective uid the same as the real uid
        setuid(getuid());
        
        // Execute /bin/sh
        v[0] = "/bin/sh"; v[1] = 0;
        execve(v[0], v, 0);
    }