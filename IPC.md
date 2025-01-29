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
    // and less than zero if fork fails
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

# Pipe
pipefd[0] is used to read from and pipefd[1] is used to write.<br>
```cpp
int main()
{
    int pipefd[2];
    if( -1 == pipe( pipefd ) )
    {
        printf( "Pipe failed" );
        exit( EXIT_FAILURE );
    }

    pid_t fpid = fork();

    if( 0 == fpid ) // Child
    {
        close( pipefd[1] );
        char readbuff[100];
        int readBytes = read( pipefd[0], readbuff, sizeof( readbuff ) );
        printf( "Child : %s, %d", readbuff, readBytes );
        close( pipefd[0] );
    }
    else    // Parent
    {
        close( pipefd[0] );
        char msg[] = "Hello from parent";
        write( pipefd[1], msg, strlen( msg ) + 1 );
        close( pipefd[1] );
    }
}
// Child : Hello from parent, 18
```
