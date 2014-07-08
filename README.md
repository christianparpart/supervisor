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
