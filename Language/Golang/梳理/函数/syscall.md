# syscall

```bash
cmd := exec.Command("/bin/sh", "-c", "...........")
// Go会将PGID设置成与PID相同的值
cmd.SysProcAttr = &syscall.SysProcAttr{Setpgid: true}
syscall.Kill(-cmd.Process.Pid, syscall.SIGKILL)
```