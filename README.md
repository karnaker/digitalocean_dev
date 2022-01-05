# How To Set Up an Ubuntu 20.04 Development Environment on DigitalOcean

This README walks you through how to setup how to set up an Ubuntu 20.04 development environment on DigitalOcean. Although it is starting off as a step-by-step guide, my long-term goal is to automate as much of this as possible, so that a dev environment can be created in minutes (if not seconds).

## Step 1 - Creating a DigitalOcean account

1. Go to [DigitalOcean](https://www.digitalocean.com/)
1. `Sign Up` for an account, or `Log In` if you already have one.

<img src="https://user-images.githubusercontent.com/35387991/148154629-ce1fe5ca-3496-47b6-bfd4-44a9c96d6eef.jpg" 
     align="center" alt="DigitalOcean Log In or Sign Up" width="665" height="33">

## Step 2 - Setting Up Your Droplet

1. Navigate to the "Create" menu in the upper right corner of your account's control panel, and click "Droplets" to open the Droplet create page.
   * The Droplet create page is where you choose your Droplet’s configuration, like its operating system, how much memory it has, and which features (like backups or monitoring) to enable.

<img src="https://user-images.githubusercontent.com/35387991/148156463-4292b682-69bc-4c41-b168-66a4b441e0fc.png" 
     align="center" alt="Create Droplet" width="260" height="673">

## Step 3 - Choosing an Image

1. Click on the option for Ubuntu 20.04 (LTS) x 64

<img src="https://user-images.githubusercontent.com/35387991/148157105-dcff4b0b-9534-4ef4-b20b-a34ef2db833c.png" 
     align="center" alt="Choose image: Ubuntu 20.04 (LTS) x64" width="249" height="268">

## Step 4 - Choosing a Plan

1. In the Choose a plan section, you can choose the amount of RAM, storage space, and CPU cores your Droplet will have. The Basic $5 per month plan is sufficient.

<img src="https://user-images.githubusercontent.com/35387991/148157465-97d91bb1-da28-4cc3-a817-e323fa3062a1.png" 
     align="center" alt="Choose plan: Shared CPU / Basic, Regular Intel with SSD, $5/month" width="247" height="517">

## Step 5 - Adding Block Storage (Optional)

1. Leave this option unselected unless you want to add block storage.

## Step 6 - Choosing a Datacenter Region

1. A good default will be preselected for you, but for the best performance and minimal latency, choose the datacenter nearest to you and your users.

## Step 7 - Selecting Additional Options

1. Enable several features: IPv6, user data, and monitoring. Enabling these later requires manual work.

<img src="https://user-images.githubusercontent.com/35387991/148158598-4d2bea9b-4b65-4a52-97ad-0b2c433a994c.png" 
     align="center" alt="Enable IPv6, user data, and monitoring" width="359" height="207">

## Step 8 - Setting Up SSH Authentication

1. Prerequisite: A pair of SSH keys. Be sure to pass in the -b 4096 flag to create a larger 4096-bit key.
1. Set up SSH authentication for your Droplet, using the pair of SSH keys you created.

<img src="https://user-images.githubusercontent.com/35387991/148159475-fec74dc0-9e4c-47c2-8ffc-74214a29194a.png" 
     align="center" alt="Authenticate with SSH keys" width="601" height="72">

## Step 9 - Finalizing and Creating Droplet

1. Select the number of Droplets you wish to create: 1
1. Name the Droplet. Consider a convention such as droplet # - image details - plan details - region details
1. Add tags to help organize your Droplets: remote-dev
1. Assign your Droplet to a project to help with organization: project-dev
1. Add backups: automatic, system-level disk images of Droplets taken weekly
1. When you are finished making your choices, click "Create Droplet." You will then be taken to a page showing a progress bar for the Droplet creation process. When the Droplet is ready, its IP address will be displayed.

<img src="https://user-images.githubusercontent.com/35387991/148160515-4bde63bf-b853-4b5a-9350-dcba0699f400.jpg" 
     align="center" alt="Finalize and create your droplet!" width="598" height="377">

## References

1. [How To Set Up an Ubuntu 20.04 Server on a DigitalOcean Droplet](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-ubuntu-20-04-server-on-a-digitalocean-droplet)
1. [Set up a Production-Ready Droplet](https://docs.digitalocean.com/tutorials/recommended-droplet-setup/)
1. [How to Set Up SSH Keys on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04)
1. [Awesome README](https://github.com/matiassingers/awesome-readme)
1. [Easily Add Images to GitHub](https://ardalis.com/add-images-easily-to-github/)
