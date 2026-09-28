# Permissions and Ownership

## Task
Give one user read-only access and another full access to a file using chmod and chown.

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
```

### 3. Set Ownership
```bash
sudo chown user_fullaccess:user_fullaccess example_file.txt
```

### 4. Set Permissions
To give owner full access (read, write, execute) and others read-only access:
```bash
chmod 744 example_file.txt
```
Or to give specific access:
```bash
# Give full access (rwx) to user_fullaccess
sudo setfacl -m u:user_fullaccess:rwx example_file.txt

# Give read-only access (r--) to user_readonly
sudo setfacl -m u:user_readonly:r-- example_file.txt
```

### 5. Verify Permissions and Ownership
```bash
ls -l example_file.txt
getfacl example_file.txt
```
