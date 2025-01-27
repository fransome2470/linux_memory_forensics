Linux Memory Forensics Guide

This guide documents the process of capturing and analyzing memory dumps in Linux systems using LiME (Linux Memory Extractor) and basic Linux analysis tools.

Prerequisites

Linux system (tested on Kali Linux)
Root access
Basic understanding of Linux commands
Required packages:
sudo apt update
sudo apt install build-essential linux-headers-$(uname -r)
Memory Capture Process
1. Install and Compile LiME
# Create directory for LiME
cd /home/[username]
mkdir LiME
cd LiME

# Clone LiME repository
git clone https://github.com/504ensicsLabs/LiME.git .

# Navigate to src directory and compile
cd src
make
2. Capture Memory
# Load the LiME kernel module and create memory dump
sudo insmod lime-$(uname -r).ko "path=/memory_dump.lime format=lime"

# Verify the dump was created
ls -lh /memory_dump.lime

# Remove the module when done
sudo rmmod lime
Memory Analysis
Since traditional forensics tools like Volatility might not always be available or compatible, we can use built-in Linux tools for analysis.

1. Basic Analysis Commands
# Look for running processes
strings /memory_dump.lime | grep -i "COMMAND" -A 5
strings /memory_dump.lime | grep -i "/bin/" | sort -u

# Check network connections
strings /memory_dump.lime | grep -i "ESTABLISHED" -B 2 -A 2
strings /memory_dump.lime | grep -i "tcp" | grep "LISTEN"

# Find user activity
strings /memory_dump.lime | grep -i "bash" | grep -i "history"
strings /memory_dump.lime | grep -i "/home/" | sort -u

# Check mounted filesystems
strings /memory_dump.lime | grep -i "/dev/sd"
strings /memory_dump.lime | grep -i "mount" | grep "/"

# Look for running services
strings /memory_dump.lime | grep -i "systemd" | grep "service"
2. Network Analysis
# Look for established connections
strings /memory_dump.lime | grep -i "ESTABLISHED" | grep -i "tcp"

# Check for specific ports
strings /memory_dump.lime | grep -A 1 "port="

# Find IP addresses
strings /memory_dump.lime | grep -E -o "([0-9]{1,3}[\.]){3}[0-9]{1,3}"

# Look for SSH connections
strings /memory_dump.lime | grep -i "sshd"
3. Process Analysis
# Find user processes
strings /memory_dump.lime | grep "/usr/bin/" | sort -u
strings /memory_dump.lime | grep "/usr/sbin/" | sort -u


Tips for Analysis


Always work on a copy of the memory dump to preserve the original evidence
Document all findings with timestamps
Look for unusual network connections or processes
Pay attention to user activities and login information
Check for any suspicious mounted filesystems or drives




Common Issues and Solutions


If LiME compilation fails:

Ensure you have the correct kernel headers installed

Try updating your system first

Check if you're in the correct directory
If memory dump fails:

Verify you have enough disk space
Check if you're using the correct kernel module name
Ensure you have root privileges
If analysis commands don't show results:

Verify the memory dump exists and isn't corrupted
Try different grep patterns
Check file permissions

Best Practices


Always capture memory as soon as possible after identifying an incident
Keep detailed documentation of all commands and findings
Use write blockers when possible
Maintain chain of custody documentation
Make multiple copies of the memory dump
Use timeline analysis when investigating incidents

References


LiME Documentation: https://github.com/504ensicsLabs/LiME
Linux Memory Forensics Tools and Techniques
Basic Linux Memory Analysis Principles
