# Linux Permissions and Ownership

## Task
Give one user read-only access and another full access to a file using `chmod` and `chown`.

## Instructions / Commands

### 1. Create a sample file
```bash
touch example_file.txt
echo "Sample content" > example_file.txt
```

### 2. Create Users / Groups (if required)
```bash
sudo useradd user_readonly
sudo useradd user_fullaccess
sudo groupadd fileaccess
sudo usermod -aG fileaccess user_readonly
```

### 3. Change Ownership using `chown`
Set the owner to `user_fullaccess` and the group to `fileaccess`:
```bash
sudo chown user_fullaccess:fileaccess example_file.txt
```

### 4. Set File Permissions using `chmod`
- Full access (`rwx` = 7) for owner (`user_fullaccess`)
- Read-only access (`r--` = 4) for group (`user_readonly` / group members)
- No access (`---` = 0) for others

```bash
chmod 740 example_file.txt
```

### 5. Verify Permissions
```bash
ls -l example_file.txt
```
Output format:
`-rwxr----- 1 user_fullaccess fileaccess ... example_file.txt`
