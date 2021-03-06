# How To Set Up an Ubuntu 20.04 Development Environment on DigitalOcean

This README walks you through how to setup how to set up an Ubuntu 20.04 development environment on DigitalOcean. Although it is starting off as a step-by-step guide, my long-term goal is to automate as much of this as possible, so that a dev environment can be created in minutes (if not seconds).

## 1. Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet

### Step 1 - Creating a DigitalOcean account

1. Go to [DigitalOcean](https://www.digitalocean.com/)
1. **Sign Up** for an account, or **Log In** if you already have one.

<img src="https://user-images.githubusercontent.com/35387991/148154629-ce1fe5ca-3496-47b6-bfd4-44a9c96d6eef.jpg" 
     align="center" alt="DigitalOcean Log In or Sign Up" width="665" height="33">

### Step 2 - Setting Up Your Droplet

1. Navigate to the "Create" menu in the upper right corner of your account's control panel, and click "Droplets" to open the Droplet create page.
   * The Droplet create page is where you choose your Droplet’s configuration, like its operating system, how much memory it has, and which features (like backups or monitoring) to enable.

<img src="https://user-images.githubusercontent.com/35387991/148156463-4292b682-69bc-4c41-b168-66a4b441e0fc.png" 
     align="center" alt="Create Droplet" width="260" height="673">

### Step 3 - Choosing an Image

1. Click on the option for Ubuntu 20.04 (LTS) x 64

<img src="https://user-images.githubusercontent.com/35387991/148157105-dcff4b0b-9534-4ef4-b20b-a34ef2db833c.png" 
     align="center" alt="Choose image: Ubuntu 20.04 (LTS) x64" width="249" height="268">

### Step 4 - Choosing a Plan

1. In the Choose a plan section, you can choose the amount of RAM, storage space, and CPU cores your Droplet will have. The Basic, Regular Intel with SSD, $10 per month plan is sufficient.

<img src="https://user-images.githubusercontent.com/35387991/148661830-31632bb7-5e5f-445f-a95f-6cb0c87e2fb7.png" 
     align="center" alt="Choose plan: Shared CPU / Basic, Regular Intel with SSD, $10/month for 2 GB memory, 1 CPU, 50 GB SSD Disk, 2 TB transfer" width="391" height="503">

### Step 5 - Adding Block Storage (Optional)

1. Leave this option unselected unless you want to add block storage.

### Step 6 - Choosing a Datacenter Region

1. A good default will be preselected for you, but for the best performance and minimal latency, choose the datacenter nearest to you and your users.

### Step 7 - Selecting Additional Options

1. Enable several features: IPv6, user data, and monitoring. Enabling these later requires manual work.

<img src="https://user-images.githubusercontent.com/35387991/148158598-4d2bea9b-4b65-4a52-97ad-0b2c433a994c.png" 
     align="center" alt="Enable IPv6, user data, and monitoring" width="359" height="207">

### Step 8 - Setting Up SSH Authentication

1. Prerequisite: A pair of SSH keys. Be sure to pass in the -b 4096 flag to create a larger 4096-bit key.
1. Set up SSH authentication for your Droplet, using the pair of SSH keys you created.

<img src="https://user-images.githubusercontent.com/35387991/148159475-fec74dc0-9e4c-47c2-8ffc-74214a29194a.png" 
     align="center" alt="Authenticate with SSH keys" width="601" height="72">

### Step 9 - Finalizing and Creating Droplet

1. Select the number of Droplets you wish to create: 1
1. Name the Droplet. Consider a convention such as droplet # - image details - plan details - region details
1. Add tags to help organize your Droplets: remote-dev
1. Assign your Droplet to a project to help with organization: project-dev
1. Add backups: automatic, system-level disk images of Droplets taken weekly
1. When you are finished making your choices, click "Create Droplet." You will then be taken to a page showing a progress bar for the Droplet creation process. When the Droplet is ready, its IP address will be displayed.

<img src="https://user-images.githubusercontent.com/35387991/148160515-4bde63bf-b853-4b5a-9350-dcba0699f400.jpg" 
     align="center" alt="Finalize and create your droplet!" width="598" height="377">

### Step 10 - Connect with your Droplet via SSH as a _root_ user

1. `ssh root@your_IP_address`
1. The terminal will then display a message like the below. Type yes and then enter the passphrase you chose when setting up SSH. You will then be logged into your Droplet as a root user. 

<img src="https://user-images.githubusercontent.com/35387991/148253072-e5ec4529-4574-4040-8e1e-0d57673e3eb8.png" 
     align="center" alt="Finalize and create your droplet!" width="556" height="96">

## 2. Perform Initial Server Setup with Ubuntu 20.04

### Step 1 - Logging in as root

1. If you are not already connected to your server as the root user, do so now.

### Step 2 - Creating a New User

1. Create a new user called, for example, vikram: `adduser vikram`
1. You will be asked a few questions, starting with the account password. Enter a strong password. The additional information (full name, etc.) is not required and you can just hit ENTER in any field you wish to skip.

### Step 3 - Granting Administrative Privileges

1. As root, run this command to add your new user to the sudo group (substitute the username with the new user you just created): `usermod -aG sudo vikram`
   * This is done to avoid having to log out of our normal user and log back in as the root account to do administrative tasks. We are setting up what is known as superuser or root privileges for our normal account. This will allow our normal user to run commands with administrative privileges by putting the word `sudo` before the command.

### Step 4 - Setting Up a Basic Firewall

1. See which applications are registered with the UFW firewall: `ufw app list`
   * OpenSSH should be listed
1. Make sure that the firewall allows SSH connections so that we can log back in next time: `ufw allow OpenSSH`
1. Enable the firewall: `ufw enable`. Type y and press ENTER to proceed.
1. Confirm that SSH connections are still allowed: `ufw status`.

### Step 5 - Enabling External Access for Your Regular User

1. Until verifying that you can log in and use sudo with your new user, stay logged in as root. This way, if you have problems, you can troubleshoot and make any necessary changes as root.
1. The following steps assume your root account uses SSH key authentication.
1. Add a copy of your local public key to the new user's ~/.ssh/authorized_keys file to log in successfully as the new user: `rsync --archive --chown=vikram:vikram ~/.ssh /home/vikram`
1. Open up a new terminal session on your local machine, and use SSH with your new username: `ssh vikram@your_server_ip`. You should be logged in to the new user account without using a password.

## 3. Upgrade Ubuntu

### Step 1 – Updating Currently Installed Packages

1. Update to the latest versions of all packages for the current release. Begin by updating the package list: `sudo apt update`
1. Next, upgrade installed packages to their latest available versions: `sudo apt upgrade`
   *  You will be shown a list of upgrades, and prompted to continue. Answer **y** for yes and press **Enter**.
1. Once it finishes, use the `dist-upgrade` command with `apt-get`, which will perform any additional upgrades that involve changing dependencies, adding or removing new packages as necessary. This will handle a set of upgrades which may have been held back by the previous `apt upgrade` step: `sudo apt dist-upgrade`

### Step 2 - Rebooting

1. Tell the shutdown command to reboot: `sudo shutdown -r now "Rebooting now"`

## 4. Connect to Your Droplet with VS Code

### Step 1 - Connect to a Droplet

1. In VS Code, select **Remote-SSH: Connect to Host...** from the Command Palette (**F1**, **Ctrl+Shift+P**)
1. Enter the same SSH with your new username, for example: `ssh vikram@your_server_ip`
1. If VS Code cannot automatically detect the type of server you are connecting to, select **Linux** manually.
1. You will be asked to pick a config file to use. You can select the default and enter your Droplet information in the format below:
   >Host `memorable_name` \
   >    HostName `00.000.00.000` \
   >    User `vikram` \
   >    IdentityFile `~\.ssh\id_rsa`

### Step 2 - Setting up the SSH Agent

1. If you are connecting to an SSH host using a key with a passphrase, you should ensure that the SSH Agent is running **locally**.
1. Follow [these instructions to start the SSH Agent](https://code.visualstudio.com/docs/remote/troubleshooting#_setting-up-the-ssh-agent). After starting the agent, be sure to restart VS Code.
1. To verify that the agent is running and is reachable from VS Code's environment, run `ssh-add -l` in the terminal of a local VS Code window. You should see a listing of the keys in the agent (or a message that it has no keys).
1. VS Code will automatically add your key to the agent so you don't have to enter your passphrase every time you open a remote VS Code window.
1. The keys must be unlocked on each first login.

### Step 3 - Prevent potential VS Code error ENOSPC

1. To help prevent a potential error ENOSPC, such as when running `npm run start` in React, where VS Code is unable to watch for file changes in a large workspace, make sure that potentially large folders are added to the `files.watcherExclude` setting.
1. Open the Settings editor from the **Command Palette** (Ctrl+Shift+P) with **Preferences: Open Settings (JSON)**.
1. Exclude specific workspace directories from the VS Code file watcher with the `files.watcherExclude` setting. The default for `files.watcherExclude` excludes `node_modules` and some folders under `.git`, but you can add other directories that you don't want VS Code to track.
   ```
   "files.watcherExclude": {
      "**/.git/objects/**": true,
      "**/.git/subtree-cache/**": true,
      "**/node_modules/*/**": true
    }
   ```

## 5. Connect to GitHub with SSH

### Step 1 - Checking for existing SSH keys

1. Enter `ls -al ~/.ssh` to see if existing SSH keys are present.

### Step 2 - Generating a new SSH key and adding it to the ssh-agent

1. Paste the following, substituting in your GitHub email address: `ssh-keygen -t ed25519 -C "your_email@example.com"`
1. When you're prompted to "Enter file in which to save the key," press Enter. This accepts the default file location.

### Step 3 - Adding your SSH key to the ssh-agent

1. Start the ssh-agent in the background: `eval $(ssh-agent)`
1. Add your SSH private key to the ssh-agent: `ssh-add ~/.ssh/id_ed25519`

### Step 4 - Adding a new SSH key to your GitHub account

1. Copy the SSH public key to your clipboard:
   * `cat ~/.ssh/id_ed25519.pub`
   * Then select and copy the contents of the id_ed25519.pub file displayed in the terminal to your clipboard
1. In the upper-right corner of any GitHub page, click your **profile photo**, then click **Settings**.
1. In the user settings sidebar, click **SSH and GPG keys**.
1. Click **New SSH key** or **Add SSH key**.
1. In the "Title" field, add a descriptive label for the new key. For example, if you're using a personal Mac, you might call this key "Personal MacBook Air".
1. Paste your key into the "Key" field.
1. Click **Add SSH key**.
1. If prompted, confirm your GitHub password.

### Step 5 - Testing your SSH connection

1. Enter the following to attempt to ssh to GitHub: `ssh -T git@github.com`
1. You may see a warning like this:
   > The authenticity of host 'github.com (IP ADDRESS)' can't be established.
   > RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
   > Are you sure you want to continue connecting (yes/no)?
1. Verify that the fingerprint in the message you see matches [GitHub's RSA public key fingerprint](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints). If it does, then type `yes`.

## 6. Install your dotfiles onto a new system

1. These steps assume you already store your configuration/dotfiles in a Git repository, and you seek to migrate them to your new Droplet.
1. Create an alias `config` which we will use instead of the regular `git` when we want to interact with our configuration repository: `alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'`
1. Make the source repository ignore the folder where you'll clone it, so that you don't create weird recursion problems: `echo ".cfg" >> .gitignore`
1. Clone your dotfiles into a `bare` repository in a "dot" folder of your `$HOME`: `git clone --bare <git-repo-url> $HOME/.cfg`
1. Define the alias in the current shell scope: `alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'`
1. Checkout the actual content from the bare repository to your `$HOME`: `config checkout`
1. The step above might fail with a message like:
   > error: The following untracked working tree files would be overwritten by checkout:
   >        .bashrc
   > Please move or remove them before you switch branches.
   > Aborting
1. If you get the above error, move all the offending files automatically to a backup folder: mkdir -p .config-backup && \
config checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | \
xargs -I{} mv {} .config-backup/{}
1. Re-run the check out if you had problems: `config checkout`
1. Set the flag `showUntrackedFiles` to no on this specific (local) repository: `config config --local status.showUntrackedFiles no`
1. You're done, from now on you can now type config commands to add and update your dotfiles, for example: `config status`

## 7. Install Miniconda on Ubuntu 20.04

### Step 1 - Retrieving the Latest Version of Miniconda

1. From a web browser, view the [Latest Miniconda Installer Links](https://docs.conda.io/en/latest/miniconda.html#latest-miniconda-installer-links)
1. Find the latest Linux version and copy the link to the installer bash script.

### Step 2 - Downloading the Miniconda Bash Script

1. `cd /tmp`
1. `curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh`

### Step 3 - Running the Miniconda Script

1. `bash Miniconda3-latest-Linux-x86_64.sh`
1. You'll receive a prompt to "press ENTER to continue." Press `ENTER`, and then continue through the document by pressing the `SPACEBAR`.
1. When you get to the end of the license, type `yes` then press `ENTER` as long as you agree to the license to complete installation.
1. Once you agree to the license, you will be prompted to choose the location of the installation. Press `ENTER` to accept the default location (or specify a different location).
1. Once installation is complete, you will be prompted "Do you wish the installer to initialize Miniconda3 by running conda init?" Type `yes` to use the `conda` command.

### Step 4 - Activating Installation

1. Activate the installation: `source ~/.bashrc`

### Step 5 - Testing Installation

1. Use the conda command to test the installation and activation: `conda list`

## 8. Install MongoDB Database Tools

_The MongoDB Database Tools are a collection of command-line utilities for working with a MongoDB deployment. These tools release independently from the MongoDB Server schedule._

### Step 1 - Installing MongoDB Database Tools

1. `cd /tmp`
1. Navigate to the [MongoDB Database Tools downloads page](https://www.mongodb.com/try/download/database-tools).
1. Find the **Available Downloads** section on the right-hand side of the page and select your desired **Version** (e.g. the latest), **Platform** (e.g. Ubuntu 20.04 x86_64), and Package (e.g. deb) from the drop-down menus there.
1. Run a wget command and pass the link you just copied to it as an argument. This will download the package to your working directory: `wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-ubuntu2004-x86_64-100.5.1.deb`
1. Then install the .deb package with apt: `sudo apt install ./mongodb-database-tools-ubuntu2004-x86_64-100.5.1.deb`

### Step 2. Test the installation

   1. `mongodump --version`

   > <img src="https://user-images.githubusercontent.com/35387991/148426061-a0a64145-1b96-4042-9f0a-219d387bc3a5.png" 
      align="center" alt="mongodump --version output" width="382" height="102">

   2. `mongorestore --version`

   > <img src="https://user-images.githubusercontent.com/35387991/148426071-de2a6186-5b28-4b22-960f-08d378160af9.png" 
      align="center" alt="mongorestore --version output" width="378" height="104">

## 9. Install redis-cli

1. Install redis-cli: `sudo apt install redis-tools`
1. Test redis-cli: `redis-cli -h <redis-server> -p <redis-port> -a <password>`

## 10. Install ngrok

1. Install ngrok via Apt:
   > curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null &&
              echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list &&
              sudo apt update && sudo apt install ngrok
1. Add authtoken: `ngrok authtoken <token>`
   * Use your personal Authtoken to authenticate the ngrok agent that you downloaded.
1. Test by starting a tunnel: `ngrok http 80`

## 11.Install the Heroku CLI

### Step 1 - Download and install

1. Install the Heroku CLI: `sudo snap install --classic heroku`

### Step 2 - Verifying your installation

1. To verify your CLI installation, use the `heroku --version` command: `heroku --version`

### Step 3 - Getting started

1. After you install the CLI, run the `heroku login -i` command.
1. You'll be prompted to enter your email.
1. You'll then be prompted to enter your password; however, if your account has MFA enabled, API requests using basic authentication with email and password are not supported. Submit your API Key instead.
   > <img src="https://user-images.githubusercontent.com/35387991/148583897-d871e716-4ec0-482b-8cbf-ed6261848255.png" 
       align="center" alt="Heroku API Key" width="377" height="147">

## 12.Install Docker Engine on Ubuntu

### Step 1 - Set up the repository

1. Update the `apt` package index and install packages to allow `apt` to use a repository over HTTPS:
   ```
   sudo apt-get update
   ```
   ```
   sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
   ```
1. Add Docker’s official GPG key: `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg`
1. Use the following command to set up the stable repository:
   ```
   echo \
   "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
### Step 2 - Install Docker Engine

1. Update the apt package index, and install the latest version of Docker Engine and containerd, or go to the next step to install a specific version:
   * `sudo apt-get update`
   * `sudo apt-get install docker-ce docker-ce-cli containerd.io`
1. Verify that Docker Engine is installed correctly by running the `hello-world` image: `sudo docker run hello-world`. This command downloads a test image and runs it in a container. When the container runs, it prints a message and exits. Docker Engine is installed and running. The docker group is created but no users are added to it. You need to use sudo to run Docker commands.

## 13.Installing Node Using the Node Version Manager

1. Visit [NVM's GitHub page](https://github.com/nvm-sh/nvm). Copy the curl command from the README file that displays on the main page. This will get you the most recent version of the installation script.
1. Before piping the command through to `bash`, it is always a good idea to audit the script to make sure it isn’t doing anything you don’t agree with. You can do that by removing the `| bash` segment at the end of the curl command: `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh`
   * Take a look and make sure you are comfortable with the changes it is making.
1. When you are satisfied, run the command again with `| bash` appended at the end to install the nvm script to your user account: `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash`
1. To use it, you must first source your `.bashrc` file: `source ~/.bashrc`
1. Now, you can ask NVM which versions of Node are available: `nvm list-remote`
1. You can install a version of Node by typing any of the release versions you see. For instance, to get version v17.3.0, you can type: `nvm install v17.3.0`
1. You can see the different versions you have installed by typing: `nvm list`
   * This shows the currently active version on the first line (-> v17.3.0), followed by some named aliases and the versions that those aliases point to.
1. You can verify that the install was successful by typing:
   * `node -v`
   * `npm -v`

## References

1. Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet
   1. [How To Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-ubuntu-20-04-server-on-a-digitalocean-droplet)
   1. [Set up a Production-Ready Droplet](https://docs.digitalocean.com/tutorials/recommended-droplet-setup/)
1. Perform Initial Server Setup with Ubuntu 20.04
   1. [Initial Server Setup with Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04)
1. Upgrade Ubuntu
   1. [How To Upgrade to Ubuntu 20.04 Focal Fossa](https://www.digitalocean.com/community/tutorials/how-to-upgrade-to-ubuntu-20-04-focal-fossa)
   1. [Command-line Basics: Shutdown and Reboot](https://www.digitalocean.com/community/tutorials/workflow-command-line-basics-shutdown-reboot)
   1. [29 packages can be updated - How?](https://askubuntu.com/questions/449032/29-packages-can-be-updated-how)
1. Connect to Your Droplet with VS Code
   1. [Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh)
   1. [Remote Development Tips and Tricks - Setting up the SSH Agent](https://code.visualstudio.com/docs/remote/troubleshooting#_setting-up-the-ssh-agent)
   1. [SSH Agent loses identity while restart machine](https://superuser.com/questions/951002/ssh-agent-loses-identity-while-restart-machine)
   1. [Visual Studio Code on Linux - "Visual Studio Code is unable to watch for file changes in this large workspace" (error ENOSPC)](https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc)
   1. [Settings - Creating User and Workspace Settings](https://code.visualstudio.com/docs/getstarted/settings#_creating-user-and-workspace-settings)
   1. [Settings - Default settings](https://code.visualstudio.com/docs/getstarted/settings#_default-settings)
1. Connect to GitHub with SSH
   1. [Connecting to GitHub with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
   1. [SSH Essentials: Working with SSH Servers, Clients, and Keys](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys)
1. Install your dotfiles onto a new system
   1. [The best way to store your dotfiles: A bare Git repository](https://www.atlassian.com/git/tutorials/dotfiles)
1. Install Miniconda on Ubuntu 20.04
   1. [How To Install Anaconda on Ubuntu 18.04 Quickstart](https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart)
   1. [Miniconda](https://docs.conda.io/en/latest/miniconda.html#latest-miniconda-installer-links)
1. Install MongoDB Database Tools
   1. [MongoDB: Choose which type of deployment is best for you](https://www.mongodb.com/try/download)
   1. [How To Install MongoDB on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-mongodb-on-ubuntu-20-04)
1. Install redis-cli
   1. [redis-cli, the Redis command line interface](https://redis.io/topics/rediscli)
   1. [How To Install Redis CLI on Ubuntu](https://www.howtodojo.com/install-redis-cli-ubuntu/)
   1. [How to Get Redis-cli Without Installing Redis Server (even on Windows)](https://redis.com/blog/get-redis-cli-without-installing-redis-server/)
1. Install ngrok
   1. [Install ngrok](https://ngrok.com/download)
1. Install the Heroku CLI
   1. [The Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)
   1. [Not able login to Heroku account from command line](https://stackoverflow.com/questions/68105084/not-able-login-to-heroku-account-from-command-line)
   1. [Heroku CLI Authentication](https://devcenter.heroku.com/articles/authentication)
1. Install Docker Engine on Ubuntu
   1. [Install Docker Engine on Ubuntu - Install using the repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
   1. [Post-installation steps for Linux](https://docs.docker.com/engine/install/linux-postinstall/)
1. Installing Node Using the Node Version Manager
   1. [How To Install Node.js on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04)
   1. [Node Version Manager](https://github.com/nvm-sh/nvm)
   1. [Downloading and installing Node.js and npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)
1. Other
   1. [How to Set Up SSH Keys on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04)
   1. [Awesome README](https://github.com/matiassingers/awesome-readme)
   1. [Easily Add Images to GitHub](https://ardalis.com/add-images-easily-to-github/)
