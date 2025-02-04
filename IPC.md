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
# Signal
```cpp
#include <signal.h>
int global{ 0 };

void sigHandler( int sig )
{
    std::cout << "Hander " << sig << std::endl;
    std::exit( EXIT_FAILURE );
}

int main()
{
    signal( SIGINT, sigHandler );

    while(1)
    {
        std::cout << "Main, ";
        ++global;
        if( 10 == global )
            raise( SIGINT );
        usleep(500000);
    }

    std::cout << "End\n";
}
//Main, Main, Main, Main, Main, Main, Main, Main, Main, Main, Hander 2
```
```cpp
#include <signal.h>

void sigHandler( int sig )
{
    std::cout << "Hander " << sig << std::endl;
    std::exit( EXIT_FAILURE );
}

int main()
{
    std::cout << "pid = " << getpid() << std::endl;
    signal( SIGINT, sigHandler );   // Signal to raise, Handler function void (*fptr)( int )

    while(1)
    {
        std::cout << "Main" << std::endl;
        usleep(500000);
    }

    std::cout << "End\n";
}

// Second program
int main()
{
    kill( 7902 , SIGINT );    // Pid of process to send the signal to, signal to send
    std::cout << "end\n";
}
```
# FIFO
```cpp
#define FIFO_FILE_NAME "/tmp/myfifo"

int main()
{
    if( -1 == mkfifo( FIFO_FILE_NAME, 0666 ) )
    {
        perror( "Error : " );
        exit( EXIT_FAILURE );
    }

    int filefd = open( FIFO_FILE_NAME, O_WRONLY );
    if( -1 == filefd )
    {
        perror( "Error : " );
        exit( EXIT_FAILURE );
    }

    char msg[] = "Hello from process 1";
    write( filefd, msg, strlen( msg ) + 1 );

    close( filefd );

    if( -1 == unlink( FIFO_FILE_NAME ) )
    {
        perror( "Error : " );
        exit( EXIT_FAILURE );
    }
}
```
```cpp
int main()
{
    std::cout << "Opening" << std::endl;
    int filefd = open( FIFO_FILE_NAME, O_RDONLY );
    if( -1 == filefd )
    {
        perror( "Error : " );
        exit( EXIT_FAILURE );
    }
    std::cout << "reading" << std::endl;

    char buff[100];
    read( filefd, buff, 100 );

    printf( "%s", buff );
}
```
