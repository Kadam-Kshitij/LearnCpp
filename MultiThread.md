## Threads

| Command | Description |
|-----------------|---------------------|
| std::thread th( func, arg1, arg2, ... ) | Create a new thread and call function func with arguments |
| th.join() | Block the calling thread till thread th completes |
| th.detach() | Detach thread th to run independantly |
| th.joinable() | Check if a thread is already joined or detached |
| std::thread( func, std::ref( a ) ) | Send reference to a thread |
