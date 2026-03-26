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
Listing 1: myprintenv.c


#### Step 3. Compare the difference of the two files using the *diff* command. Please draw your conclusion
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



### Task 3: Environment Variables and *execve()*



### Task 4: Environment Variables and *system()*



### Task 5: Environment Variable and Set-UID Programs

### Task 6: The PATH Environment Variable and Set-UID Programs


### Task 7: The *LD_PRELOAD* Environment Variable and Set-UID Programs


### Task 8: Invoking External Programs Using *system()* versus *execve()*


### Task 9: Capacity Leaking
