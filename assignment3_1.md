# DigitalOcean Droplet Creating

1. Click **create**
   ![](/images/DO_create.png)
2. Choose Closer Region
3. Choose **Debian**
4. Choose Size **Basic** & **Premium AMD** $7/mo
5. Set SSH Key
6. Change **Hostname**
   ![](/images/Hostname.png)
7. Click **create droplet**

# Create A New Regular User

## Connecting to Droplet from Local Machine

1. Open the local terminal or PowerShell on your machine.
2. Enter the command:
   ```
   ssh -i C:\User\user_name\.ssh\key_folder root@your_droplet_ip
   ```
   Make sure to replace "user_name" with your actual user name, "key_folder" with the folder containing your SSH key, and "your_droplet_ip" with the IP address of your Droplet.
3. When prompted with "Are you sure you want to continue connecting (yes/no/[fingerprint])?" respond with "yes."
4. Upon seeing "root@your_hostname:~#", it indicates a successful connection.

## Create a New Regular User

In terminal or Powershell enter the command:

```
useradd your_username
```

- ### User perform administrative tasks
  Add regular user to **sudo** group
  ```
  usermod -aG sudo your_username
  ```
- ### User has bash as login shell
  1. Check your shell
     ```
     grep your_username /etc/passwd
     ```
  2. If you didn't see /bin/bash, enter the command:
  ```
  chsh -s /bin/bash
  ```
  Then enter your password, and go to the first step to check your shell.
- ### User can access the server via SSH

  1. Copy the `.ssh` directory from the root users home directory to the new users home directory (including any files in the directory).

  ```
  sudo cp -r /root/.ssh /home/regular_username/
  ```

  2. Change ownership of the directory

  ```
  sudo chown -R regular_username:regular_user_group /home/regular_username/.ssh

  ```

  Now you can connect to Droplet Regular User from Local Machine by the command:

  ```
  ssh -i C:\User\user_name\.ssh\key_folder your_regular_username@your_droplet_ip
  ```

## Prevent the root user from connecting to the server via SSH

1. Open `sshd_config` file

```
sudo vim /etc/ssh/sshd_config
```

2. click `i` to insert version then looking for the line `PermitRootLogin yes` and change yes, to no.
   ![](/images/prime_login.png)
3. `Esc` then `:wq` to save the file and restart the ssh service

```
sudo systemctl restart ssh.service
```

## Install nginx

```
sudo apt install nginx
```

## Configure nginx to serve a sample website

Before you touch configuration files **stop** your nginx

```
sudo systemctl stop nginx
```

1. Create a new directory in `/var/www`
   e.g

```
mkdir /var/www/my-site
```

`cd` to the new directory just made and then create the web page in it
e.g.

```
vim index.html
```

2. Create a new file in /etc/nginx/sites-available
   e.g.

```
sudo vim /etc/nginx/sites-available/nginx.conf
```

3. Pass this in the file

```
server {
	listen 80 default_server;
	listen [::]:80 default_server;

	root /var/www/html;

	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}
```

Don't forgot to change the path to the directory you made on step 1
e.g.
![](/images/root.png)

4. Create a symbolic link to your new config file in `/etc/nginx/sites-enabled`

```
sudo ln -s /etc/nginx/sites-available/the_file_step2_created /etc/nginx/sites-enabled/
```

You can `cd` to `sites-enabled` to check does `the_file_step2_created` exist now 5. Restart your nginx

```
sudo systemctl start nginx
```

You can check does it start or not by

```
sudo systemctl status nginx
```

Should able to see **active**
![](/images/status.png)

5. Use `curl your_droplet_ip` or type your_dorplet_ip on the browser
   If you can see the web you put in the directory you created on step 1.
   **You success**
   ![](/images/hi.png)
