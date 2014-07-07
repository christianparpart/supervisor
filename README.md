## supervisor

`supervisor` supervises a single process.
It ensures to preserve the same PID of that process through any arbitrary
number of restarts, executable upgrades, and crashes.

### Examples

```
supervisor -- /usr/sbin/x0d -X -f /etc/xzero/x0d.conf
```
