# 2020 Rebuild Process Documentation

In Mid-June we got nailed by lightning again.  This time it took out both the Pi and the SD card.
It is, of course, in moments like this that you discover you failed yet again to document changes you've made incrementally to the system.

So, this time (at least) I'm going to document the steps it takes to get from where my last Filezilla copies left me to where I need to be.  

Let's begin:

- Download Raspberry Pi Imager for Windows
  - <https://www.raspberrypi.org/downloads/>
- Install Imager on Windows machine
- Run Imager and write selected OS to SD card
- Start following the steps I wrote out at: <https://github.com/casspop/ohd/blob/master/SetupRaspianForOhd.md>
- Insert SD card and boot Pi
  - Login - username: pi, password: raspberry
- Run "sudo raspi-config" at command prompt
        Make changes to Network, Hostname, localization, timezone, enable SSH
- Reboot.
- Install pip3
- Install RPi.GPIO module for Python
- Change default user/group and password
  - Create root user password
  - ```$ sudo passwd root```
  - Log out and back in as root
  - ```$ logout```
  - Change user name
  - ```$ usermod -l newname pi```
  - Change user group.  Start by finding out what groups the oldname belongs to.
  - ```$ groups oldname```
  - Edit /etc/group manually to change 'pi' to 'newname'.  This is the primary group.
  - ```$ sudo nano /etc/group```
  - Change all the Secondary groups at once by:  (leave off the first one, which was 'pi')
  - ```$ usermod -G comma,delimited,list,of,groups,from,the,last,step newname```
  - Create a new /home/directory and move newuser's files over there.
  - ```$ usermod -m -d /home/newname newname```
  - Log out of root's account and into the newname account
  - ```$ logout```
  - Change newname's password
  - ```$ passwd```
  - Run apt-get update to verify sudo permissions are working.
  - ```$ sudo apt-get update```
  - Remove the root password.  You really don't need it now.
  - ```$ sudo passwd -l root```

- Set a static IP address.
  - ```$ sudo nano /etc/dhcpcd.conf```
  - Edit the hostname at the top of the file to match your hostname.
  - Add these lines to the bottom:
        ```interface eth0```

        ```static ip_address 192.168.1.###/24  (### is your number)```

        ```static routers=192.168.1.###  (### is your router's address)```

        ```static domain_name_servers=192.168.1.### #.#.#.# (typically your router and your other favorite)```
        
  - Save and reboot.  You should see the new IP address.  If not, do:
  - ```$ ip addr```