# How To Set Up an Ubuntu 20.04 Development Environment on DigitalOcean

This README walks you through how to setup how to set up an Ubuntu 20.04 development environment on DigitalOcean. Although it is starting off as a step-by-step guide, my long-term goal is to automate as much of this as possible, so that a dev environment can be created in minutes (if not seconds).

## Summary

1. [Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet](#set-up-an-ubuntu-20.04-server-on-a-digitalocean-droplet)
1. [x]
1. [Connect to Your Droplet with VS Code](#connect-to-your-droplet-with-vs-code)

## I. Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet

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

1. In the Choose a plan section, you can choose the amount of RAM, storage space, and CPU cores your Droplet will have. The Basic $5 per month plan is sufficient.

<img src="https://user-images.githubusercontent.com/35387991/148157465-97d91bb1-da28-4cc3-a817-e323fa3062a1.png" 
     align="center" alt="Choose plan: Shared CPU / Basic, Regular Intel with SSD, $5/month" width="247" height="517">

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

## II. Perform Initial Server Setup with Ubuntu 20.04

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

## III. Connect to Your Droplet with VS Code

### Step 1 - Connect to a Droplet

1. In VS Code, select **Remote-SSH: Connect to Host...** from the Command Palette (**F1**, **Ctrl+Shift+P**)
1. Enter the same SSH with your new username, for example: `ssh vikram@your_server_ip`
1. If VS Code cannot automatically detect the type of server you are connecting to, select **Linux** manually.
1. You will be asked to pick a config file to use. You can select the default and enter your Droplet information in the format below:
   >Host `memorable_name` \
   >    HostName `00.000.00.000` \
   >    User `vikram` \
   >    IdentityFile `~\.ssh\id_rsa`

## IV. Connect to GitHub with SSH

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

## V. Install Miniconda on Ubuntu 20.04

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

## VI. Install your dotfiles onto a new system

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

## References

1. I. Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet
   1. [How To Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-ubuntu-20-04-server-on-a-digitalocean-droplet)
   1. [Set up a Production-Ready Droplet](https://docs.digitalocean.com/tutorials/recommended-droplet-setup/)
1. II. Perform Initial Server Setup with Ubuntu 20.04
   1. [Initial Server Setup with Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04)
1. III. Connect to Your Droplet with VS Code
   1. [Remote Development using SSH](https://code.visualstudio.com/docs/remote/ssh)
1. IV. Connect to GitHub with SSH
   1. [Connecting to GitHub with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
   1. [SSH Essentials: Working with SSH Servers, Clients, and Keys](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys)
1. V. Install Miniconda on Ubuntu 20.04
   1. [How To Install Anaconda on Ubuntu 18.04 Quickstart](https://www.digitalocean.com/community/tutorials/how-to-install-anaconda-on-ubuntu-18-04-quickstart)
   1. [Miniconda](https://docs.conda.io/en/latest/miniconda.html#latest-miniconda-installer-links)
1. VI. Install your dotfiles onto a new system
   1. [The best way to store your dotfiles: A bare Git repository](https://www.atlassian.com/git/tutorials/dotfiles)
1. Other
   1. [How to Set Up SSH Keys on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04)
   1. [Awesome README](https://github.com/matiassingers/awesome-readme)
   1. [Easily Add Images to GitHub](https://ardalis.com/add-images-easily-to-github/)
