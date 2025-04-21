ooooooo again ipadddd
The fork method splits the current execution of the code into a child and parent process.
- Parent process: The process which was already running the code
- Child process: Newly spawned process with its own process ID

The fork command effectively forks the current process into two and the return value of the call differs between the two processes.

The fork call returns either: 

| Value            | In Process | Description                                                                                     |
| ---------------- | ---------- | ----------------------------------------------------------------------------------------------- |
| Negative integer | -          | Process creation failed                                                                         |
| 0                | Child      | The child process gets returned a 0 so it knows it is the newly created process.                |
| Positive integer | Parent     | Parent process receives the child processes process ID to know that it is the parent process.2c |
c |

