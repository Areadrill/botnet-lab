# *Botnet Lab*

## Summary

The botnet built using this laboratory will match the general architecture for any botnet based on a Command-and-Control (C&C) architecture.
Our actor is the *Bot Herder* or *Bot Master*, it operates using the a special IRC client (that is part of this laboratory), connects to a IRC-Server (in this case a IRCD-Hybrid based one) where all the bots are connected.

Whenever the *Bot Herder* sends a message to the IRC Server it broadcasts it to all the connected bots, which will then execute the requested job.

Special cases are the Spam request and the Screenshot/Webcam request. 
In the first case, the Spam, to avoid the trouble of setting up a SMTP server on all the bots we use the [Mandrill API](https://www.mandrill.com/) for sending the e-mail. While this can appear strange, 
because of centralizing all the traffic on one e-mail sender API with low free quotas and with the risk of the account being blocked, we send the API Key in the request sent to bots in a way that if a Key is blocked we can simply send a different API Key. 
Additionally it ussd the [PasteBin Service](http://pastebin.com/) and it's "anonymous and hidden file" capabilities for hosting relevant data like the the *e-mail sending list*, *API Key* and *Message* and the *Bot Herder* just needs to send the file's URL to the bots. 

In the second case, the Screenshot/Webcam, the bots uses the [Imgur API](https://api.imgur.com/) for storing images and sends only the URL of those images back to the *Bot Herder*.

Adding to all of this, [freegeoip](https://freegeoip.net/) and [Google Static Map API](https://developers.google.com/maps/documentation/static-maps/) are used in order to get and show the position of the controlled hosts relative to a worldwide map.

Also, RSA encryption is used so that the *Bot Herder* is the only one capable of decrypting the messages sent by the bots because it is the *Private Key* owner. The bots encrypt the messages using the *Public Key* defined by the *Bot Herder*.


## *Botnet Lab* setup

### Minimal requirements

#### Operative System

Any Windows or Linux system will be capable of building and running the project.

#### Dependencies

- [Python 2.7.x](https://www.python.org/downloads/)
- [pip 7.x.x]()
	- Download [get-pip.py](https://bootstrap.pypa.io/get-pip.py)
	- `python get-pip.py`
- [venv - Virtual Environments](https://docs.python.org/3/library/venv.html)
	- Windows: `pip install virtualenv`
	- Linux: `sudo pip install virtualenv`
- [git](https://git-scm.com/)
	- [Install instructions](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
	
## Setup guide

Almost every one of these steps are commands.

#### Cloning the repository

- `git clone https://github.com/jpdias/botnet-lab.git`
- `cd botnet-lab/botnet-lab`

#### Setting up the virtual environment

- `virtualenv venv`
- Starting the created virtual envoirnemnt
	- Linux: `. venv/bin/activate`
	- Windows: `venv\scripts\activate` 
	
### Installing project dependencies

- `pip install -r requirements.txt`
	 - This probably will fail at some point, because some of the used libraries are no longer supported or development has been halted.

So, for each line of the requirements.txt do:

- `pip install <requirement>`

If any dependency fails, try:
- `sudo apt-get install python2.7-dev`
- `pip install <requirement> --allow-external <requirement> --allow-unverified <requirement>`
	
If it fails too, the best workaround is to search for a valid package. For example the dependency `pygame==1.9.2a0` will fail to install. 

**Windows**

To make it install on Windows simply go to the [Unofficial Windows Binaries for Python Extension Packages](http://www.lfd.uci.edu/~gohlke/pythonlibs/) from the [Univeristy of California](http://www.uci.edu/) and use the respective wheel.
For example with `pygame==1.9.2a0` for Windows 64bit:
- `pip install <path_to_download_folder>/pygame‑1.9.2a0‑cp33‑none‑win_amd64.whl`

**Linux**

In Linux there is no generic way of installing all the missing packages. For that we will go through every one that may cause problems and present a workaround.

- *pygame*:
For installing `pygame` you can use the shell script below.
Remember to do this with `venv` active.
```
#!/bin/sh
BASE_PATH=`pwd`
sudo apt-get build-dep python-pygame
sudo apt-get install python-dev libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev libsdl1.2-dev libsmpeg-dev python-numpy subversion libportmidi-dev ffmpeg libswscale-dev libavformat-dev libavcodec-dev libv4l-dev
cd /usr/include/linux
sudo ln -s ../libv4l1-videodev.h videodev.h
cd $BASE_PATH
wget http://www.pygame.org/ftp/pygame-1.9.1release.tar.gz
tar -xzf pygame-1.9.1release.tar.gz
cd pygame-1.9.1release
python config.py
sudo python setup.py install
cd $BASE_PATH
rm pygame-1.9.1release.tar.gz
sudo rm -rf pygame-1.9.1release
ln -s /usr/local/lib/python2.7/dist-packages/pygame venv/lib/python2.7/site-packages/pygame
```

- *python-xlib*: `sudo pip install svn+https://svn.code.sf.net/p/python-xlib/code/trunk/`
	- You can have to install svn  with `sudo apt-get install svn`
		
- *autopy*: `sudo apt-get install libxtst-dev`

Some of the libraries like `pyHook` and `pypiwin32` are Windows-only dependencies, so if you are on a Linux machine don't worry about them.
