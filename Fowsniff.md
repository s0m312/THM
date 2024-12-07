>nmap revealed port `22,80,110,143` <br> (port 80 revealed an osint hint, so i looked on google for it and found this clue: [Here](https://github.com/berzerk0/Fowsniff/blob/main/fowsniff.txt#L19C1-L27C49) )
>cracked the passes on crackstation and ran metasploit module `auxiliary/scanner/pop3/pop3_login`<br>

>`seina:scoobydoo2` came back possitive, 
>by looking threw the emails ,<br> the admin sent them default new ssh pass, `S1ck3nBluff+secureshell`<br>
>by taking all the users names he sent to, i've ran a bruteforce to see who it matches,
>`baksteen:S1ck3nBluff+secureshell`came back possitive<br>
>Logged in, and uploaded linpeas to enumerate, the groups a file popped up, /opt/cube/cube.sh ,<br> turns out he run as root by other file called `/etc/update-motd.d/00-header` as soon as someone logs into ssh.<br>
>took a python reverse shell by 'Pentest Monkeys' and added to the 'cube.sh' file (at the end)
>opened listener and re-logged in into the ssh and a root shell spawned.
