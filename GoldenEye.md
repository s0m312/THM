Guided:

##            Task 1 - Intro & Enumeration
**Q:** Use nmap to scan the network for all ports. How many ports are open?

**A:** 4      
> (used nmap -p- , but didn't count the filtered ones)

**Q:** Who needs to make sure they update their default password?
**A:** Boris
> Opened Web-site on port 80, used `Ctrl+U` and saw a link to "terminal.js",
> The link reveals HTML enteties encode for user Boris default password.
`&#73;&#110;&#118;&#105;&#110;&#99;&#105;&#98;&#108;&#101;&#72;&#97;&#99;&#107;&#51;&#114`<br>

**Q:** Whats their password? <br>
**A:** InvincibleHack3&#114
> By decoding it online [Here](https://codebeautify.org/html-decode-string)

##            Task 2 - It's mail time...
**Q:** If those creds don't seem to work, can you use another program to find other users and passwords? Maybe Hydra?Whats their new password? <br>
**A:** secret1!
>Used Telnet to connect to pop3 on port 55007, `boris:InvincibleHack3&#114` didn't work, but at least I've got the username right, time for bruteforce. <br>
``hydra -l boris -P /usr/share/wordlists/fasttrack.txt <IP> pop3 -s 55007 -t 50`` <br>

**Q:** Inspect port 55007, what services is configured to use this port?<br>
**A:** Telnet

**Q:** What can you find on this service?<br>
**A:** emails

**Q:** What user can break Boris' codes?<br>
**A:** Natalya<br>
`Mentioned at web-site source and emails`<br>
``hydra -l natalya -P /usr/share/wordlists/fasttrack.txt <IP> pop3 -s 55007 -t 50`` <br>
>Reveals `natalya:bird`<br>
>natalya's email reveals this:<br>

`username: xenia`<br>
`password: RCP90rulez!` <br>

`And if you didn't have the URL on outr internal Domain: severnaya-station.com/gnocertdir`
<br>
##            GoldenEye Operators Training
**Q:** Try using the credentials you found earlier. Which user can you login as?<br>
**A:** xenia <br>

**Q:** Have a poke around the site. What other user can you find?<br>
**A:** doak<br>
>Going to `http://severnaya-station.com/gnocertdir` with xenia's creds,<br>
>Go to `My profile` -> `Messages` reveals msg from Dr_doak<br>
`My email username is... doak`<br>

**Q:** What was this users password?<br>
**A:** goat
`hydra -l doak -P /usr/share/wordlists/fasttrack.txt <IP> pop3 -s 55007 -t 50`

**Q:** What is the next user you can find from doak?<br>
**A:** dr_doak<br>
>Reveals in he's email.

**Q:** What is this users password?<br>
**A:** 4England!
>Login as user doak, go to `My profile` -> `My private files` -> `for james` and download the file "s3cret.txt"<br>
>S3cret.txt reveals `/dir007key/for-007.jpg` , so i went to the full link ->
>`http://severnaya-station.com/dir007key/for-007.jpg` -> Download the .img and pass it on to extract meta-data [Here](https://jimpl.com/) <br>
>Exifdata reveals base64 hidden message `eFdpbnRlcjE5OTV4IQ==` <br>
`echo eFdpbnRlcjE5OTV4IQ== | base64 -d` -> `xWinter1995x!`<br>
>It was web-site's admin password, `admin:xWinter1995x!`<br>
>Login as admin and THM hinted to look for "Aspell" plugin.<br>
>There was a search bar on the bottom-left side, searched for 'spell' and found "Spell engine" that should of be changed from 'Google Spell' to 'PSpellShell', and 'Path to aspell' changed to my reverse shell, (THM hinted it should be python one)<br>
>[Pentestmonkey shells](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) (python one) <br>
>Opened nc listener and got connection back as user `www-data`<br>
>THM suggested to download and transfer [linuxprivchecker](https://gist.github.com/sh1n0b1/e2e1a5f63fbec3706123)<br>
> Honestly they just wanted me to use `uname -a` <br>
**Q:** Whats the kernel version?<br>
**A:** 3.13.0-32-generic<br>
>THM said to use this [Exploit](https://www.exploit-db.com/exploits/37292)<br>
>But the target machine didn't have "gcc" to compile it, but it had "cc" , All i had to do to tweak it correctly is to open the exploit and change from `gcc` -> `cc`<br>
>Upload to target, compile the exploit, use exploit... and... <br>
`id`<br>
`uid=0(root) gid=0(root) groups=0(root),33(www-data)`
<br>
>Get root's flag and open the last link in it -> http://severnaya-station.com/006-final/xvf7-flag/


