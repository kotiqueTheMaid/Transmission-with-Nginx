# Transmission-with-Nginx
This guide covers how to setup a Transmission torrent server with Nginx port forwarding SSL enabled.
### Table of contents: 
- [Prerequisites](#prereq)
- [Transmission Setup](#tsetup)
	- [Install Transmission daemon.](#tinstall)
	- [Configure Transmission daemon.](#tconf)
- [Nginx Setup](#nsetup)
	- [Installing server](#ninstall)
	- [Obtaining certificates](#obcert)
- [Running Transmission with Nginx](#runtr)

### <a name="prereq"></a> Prerequsites

* Ubuntu server
* Opened ports 80, 443
* Server DNS name
* Root Access


### <a name="tsetup"></a> Transmission Setup
I used this [guide](https://help.ubuntu.com/community/TransmissionHowTo) for a reference.

Step 1: Install Transmission daemon. <a name="tinstall"></a>

Add the latest PPA repository: `$ sudo add-apt-repository ppa:transmissionbt/ppa`
Update repositories and install transmission: `$ sudo apt-get install transmission-cli transmission-common transmission-daemon`.

Step 2: Configure  Transmission Daemon<a name="tconf"></a>.

Before making any changes stop the daemon. 
`$ sudo systemctl stop transmission-daemon`.

Daemon settings are defined in `/var/lib/transmission-daemon/info/settings.json`.

Make a copy of default configuration file to recover from it in case of broken configuration.
`$ cp /var/lib/transmission-daemon/info/settings.json /var/lib/transmission-daemon/info/settings_copy.json`.

Edit config with any desired text editor. I am going to use nano. `$ sudo nano /var/lib/transmission-daemon/info/settings.json`

Setting <em>rpc-password</em> contains hashed password from your rpc.`"rpc-password": "{62b16db87b89a91dd49a5110a7cafc06d20eb4f2wtK6kqPj",` 

Change it to any desired password. It will get hashed after the daemon starts.

``"rpc-password": "example_password",``
Optional: change your username.
`"rpc-username": "example_usernmae",`.

Restart `transmission-daemon`
`$ sudo systemctl restart transmission-daemon`.

### Nginx Setup <a name="nsetup"></a>
Step 3: Installing Nginx server <a name="ninstall"></a>

Follow this official [guide](https://nginx.org/en/linux_packages.html) from nginx.org.

Step 4: Obtaining certificates <a name="obcert"></a>

Navigate to [certbot website](https://certbot.eff.org/instructions?ws=nginx&os=snap) and follow the steps. **Important** use `$ sudo certbot --nginx` to install certificates. 

Memorize paths for your certificates, normally they can be located at `/etc/letsencrypt/live/your.domain.name/`

Now try to access your server with its URL. It should display nginx default webpage with secure connection enabled.

### Running Transmission with Nginx<a name="runtr"></a>
Navigate to `$ cd /etc/nginx/sites-enabled`

Delete default site that we saw when we were testing ssl connectivity `$ rm default `.

Create a file called `transmission` `$ touch transmission`.

Edit the file `$ nano transmission`.

I used configuration that I found in [thread on github](#https://gist.github.com/Belphemur/47f76c40defef0269615) posted by [Zen3515](#https://gist.github.com/Zen3515). Copy [this](#https://pastebin.com/htVrnpbD) from pastebin into your `transmission` file and edit server name and ssl certificates paths.

Reload your Nginx server `$ sudo systemctl reload nginx`.

Now your transmission rpc page should be available at the URL and port that u  set in your configuration file.
