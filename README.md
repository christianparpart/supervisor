
## supervisor

`supervisor` supervises a single process.
It ensures to preserve the same PID of that process through any arbitrary
number of restarts, executable upgrades, and crashes.

### Features

 * preserves the supervisor-PID over your application for easy tooling with init systems
 * keeps track application PID changes
   * by a manual hint of `--child-pidfile=PATH`, or
   * by using Linux Control Groups (cgroups) if available.
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
  supervisor [supervisor options] -- cmd [command options ...]

options:
  -f,--fork             fork supervisor into background
  -p,--pidfile=PATH     location to store the current supervisor PID
  -r,--restart-limit=N  automatically restart program, if crashed
  -d,--restart-delay=N  number of seconds to wait before we retry
                        to restart the application
  -R,--restart-on-error Restart the application also on normal
                        termination but with an exit code != 0.
  -s,--signal=SIGNAL    Adds given signal to the list of signals
                        to forward to the supervised program.
                        Defaults to (INT, TERM, QUIT, USR1, USR2, HUP)
  -P,--child-pidfile=PATH
                        Path to the child process' managed PID file.
                        The supervisor is watching this file for updates.
  -v,--version          Prints program version number and exits
  -h,--help             Prints this help and exits.

Examples:
    supervisor -- /usr/sbin/x0d --no-fork
    supervisor -p /var/run/xzero/supervisor.pid -- /usr/sbin/x0d \
               --no-fork
```

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
