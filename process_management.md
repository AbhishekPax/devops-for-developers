# Process Management

## Finding and Killing a Process

1. Find the PID using ps and grep:
```bash
ps aux | grep <process_name>
```

2. Terminate the process using kill:
```bash
kill -9 <PID>
```
