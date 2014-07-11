
## supervisor

`supervisor` supervises a single process.
It ensures to preserve the same PID of that process through any arbitrary
number of restarts, executable upgrades, and crashes.

### Features

 * preserves the supervisor-PID over your application for easy tooling with init systems
 * keeps track application PID changes by using Linux Control Groups (cgroups).
 * restarts your application on crashes
 * minimalisticly not overbloated with unnecessary features

### Examples

```
supervisor -- /usr/sbin/x0d -X -f /etc/xzero/x0d.conf

supervisor -- /usr/bin/haproxy -f /etc/haproxy/haproxy.cfg
```

### Usage

```
supervisor: a process supervising tool
  (c) 2009-2014 Christian Parpart <trapni@gmail.com>

usage:
  supervisor [supervisor options] -- /path/to/app [app options ...]

options:
  -f,--fork             fork supervisor into background
  -p,--pidfile=PATH     location to store the current supervisor PID
  -u,--user=NAME        drops application user-privileges
  -g,--group=NAME       drops application group-privileges
  -l,--delay-limit=N    maximum delay to sleep between restarts [80]
  -e,--restart-on-error Restart the application also on normal
                        termination but with an exit code != 0.
  -c,--restart-on-crash restart application on crash (SIGSEGV)
  -q,--quiet            decreases verbosity level,
                        use -qq to void runtime errors too
  -v,--version          Prints program version number and exits
  -h,--help             Prints this help and exits.

Examples:
    supervisor -c -- /usr/sbin/x0d
    supervisor -p /var/run/xzero/supervisor.pid -- /usr/sbin/x0d \
               --no-fork
```

### Known Caveats

 * The project is still pretty young, so expect things to break.
 * Application PID tracking via `cgroups` requires `supervisor` to run
   as root. But that's not a problem for now as the application is meant to
   drop root (or get dropped) anyways.

### License

```
Copyright (c) 2009-2014 Christian Parpart <trapni@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
