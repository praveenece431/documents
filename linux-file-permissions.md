# Understanding File Permissions in Linux

### In Linux, each file and directory has an associated set of permissions that determine who can read, write, or execute them. Permissions are divided into three categories:

1. User (u) - The owner of the file.
2. Group (g) - The group associated with the file.
3. Others (o) - All other users.

#### Permissions are represented by a combination of letters and dashes. For example:

```bash
-rwxr-xr--
```

#### How it looks like is:

1. The first character indicates the type of file:
   * `-` for a regular file
   * `d` for a directory
   * `l` for a symbolic link
   * `c` for a character device
   * `b` for a block device

#### The next three characters represent the owner's permissions (user), Group and Others respectivitly:
   * r for read
   * w for write
   * x for execute

#### Numeric (Octal) Representation:
   * r --> 4
   * w --> 2
   * x --> 1
   * - (no permission) is 0

   * Total --> 7. Which means 7 is the full permissions.
```bash
 (rwx) = 4 (read) + 2 (write) + 1 (execute)
 (rw-) = 4 (read) + 2 (write)
 (r-x) = 4 (read) + 1 (execute)
 (r--) = 4 (read)
```

#### Examples:
```bash
chmod u+x file.txt
chmod g-w file.txt

# Set permissions for all (user, group, others) at once:
chmod 755 example.txt

# Check the updated permission:
ls -l file.txt
```

