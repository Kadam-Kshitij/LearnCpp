# PID
```cpp
int main()
{
    pid_t pid = getpid();
    printf( "Process ID : %d", pid );   // Process ID : 24828
}
```
# Fork
```cpp
int main()
{

    pid_t pid = getpid();
    std::cout << "Process Id : " << pid << std::endl;   // Process ID : 24828

    pid_t fpid = fork();
    // Child process starts executing from here.
    // fpid is zero for child process,
    // and for parent process it is equal to child process pid
    if( 0 == fpid )
    {
        pid_t pid = getpid();
        printf( "Process ID child : %d\n", pid );
        pid_t ppid = getppid();
        printf( "Parent Process ID child : %d\n", ppid );
    }
    else
    {
        pid_t pid = getpid();
        printf( "Process ID parent : %d\n", pid );
        printf( "fpid parent : %d\n", fpid );
    }
}
//Process Id : 26035
//Process ID parent : 26035
//fpid parent : 26036
//Process ID child : 26036
//Parent Process ID child : 3692
```
