
BRAINSTORMING
-------------

- helper programs:
  - `forkn_mt -f N -w N -t M`
    - `-f N` number of chained forks
    - `-w N` number of spreaded forks (worker processes)
    - `-t N` number of threads (worker threads)
- micro tests:
  - `pid_t getppid(pid_t pid)`
    - `assert( getppid(getpid()) == getppid() )`
  - `PidTracker.collectAll()`
  - `PidTracker.findMainPID()`
- macro tests:
  - crashing app
  - forking app
  - non-forking app to crash
