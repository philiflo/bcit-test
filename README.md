# Creating a remote server with DigitalOcean

## Preface
This guide will provide you with the steps needed to start up and run a remote server that is backed with a cloud service provider. For this scenario, we will be using DigitalOcean, which is a modular and extensive cloud service provider that is used by various businesses for their website usage.

## Step 1 - Downloading and Uploading the DigitalOcean Image

The first step is to download arch linux onto DigitalOcean. We will do this by downloading the Arch Linux image that corresponds to our use, and uploading it onto our DigitalOcean account.

### Downloading the image

- Follow this link on your internet browser: https://gitlab.archlinux.org/archlinux/arch-boxes/-/packages/1545

This will lead you to a gitlab page that contains the Arch Linux image you'll need to download

- From here, navigate to the most recent **images** folder. You'll see a long list of Arch Linux images, for this tutorial we want to download the image that ends with the **.qcow2** extension (it will be approximately 496mb)

### Uploading the image

- Once you have your image downloaded, go back to your DigitalOcean homepage. Navigate to the **Backups & Screenshots** option located on the sidebar. From here, navigate to the Custom Images tab.

- In the main module, click the **Upload Image** option and upload the Arch Linux image you've downloaded.

- Once here you will be given choices of different regions and a distributor to select. From here (assuming you are on the West Coast), select **Arch Linux** and select **San Francisco.**

## Step 2: Create the SSH Key Pair and Authenticate it
DigitalOcean utilizes a key pair system to authenticate users and connect clients and a remote machine (for this situation, a cloud service). 

This system is much more secure than the usual password system as key pairs contain a complex encrypted message, which serve as a better line of defense towards brute attacks.

### Create your SSH Key Pair
- Open your Terminal and enter one of the following commands:

If you are using the Powershell -

```ssh-keygen -t ed25519 -f C:\Users\your-user-name\.ssh\do-key -C "youremail@email.com"```

If you are using a MacOS shell -

```ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "your-email-address"```

For both scenarios, replace "your-email" with the email you used to make your DigitalOcean account, and "your-username" with your DigitalOcean username. If you would like to change the name of your droplet, you can change 'do-key' to your desired name. 

- From here, you will be prompted for a passphrase for security. Remember this passphrase as you will need it to access your key.

### Access your public key
- In the terminal, enter one of the following commands:

For Powershell users -

```Get-Content C:\Users\your-user-name\.ssh\do-key.pub | Set-Clipboard```

For MacOS users -

```pbcopy < ~/.ssh/do-key.pub```

Once entered, copy the text that is returned.

- Once here, go back to the DigitalOcean console and navigate to the **Security** tab, it is located in the main module in the **Settings** option located on the sidebar. From here, click the 'Add SSH Key' option.

- In the **Public Key** textbox, enter the text you copied from the terminal. You are also prompted for a key name, enter a name that is appropriate for the servers use. 

It is important to distinguish this public key name from the other ones that may be connecting to this server. Make sure your name is unique to your use. 

## Step 3: Create a new Arch Linux droplet
An Arch Linux droplet in DigitalOcean is the server that we will be connecting to. It requires connecting the SSH key pair that we made earlier for authentication.

### Make a new Arch Linux droplet
- In the DigitalOcean homepage, navigate to the **Create** button at the top of your screen. From there, select the **Droplet** option. 

- Once here, you will be prompted for various settings:

**Choose Region** - San Francisco

**Datacenter** - SF03

**Choose an Image** - Locate your downloaded Arch Linux image you downloaded from earlier, it will be under the 'Custom Image' tab.

**Choose Size** - From here you can choose a droplet type for prefered optimization. If you are looking to save money with this droplet, you can select the 'basic' option. From there, select your prefered CPU option, the cheapest option under 'Regular Intel' should be adequate for regular use. 

**Backups** - Enable backups for more fault security

**Choose Authentication Method** - SSH Key, select the key pair you made from earlier

**Finalize Details** - From here, enter your prefered hostname. Under projects, select the project you wish to connect this droplet to.

After you've entered all these details, click **Create Droplet**.

### Connect to your Arch Linux droplet

- In the DigitalOcean homepage, navigated to the project you connected your droplet to. In the main line you will find a set of numbers resembling an IP address, copy this.

- Once copied, navigate back to the terminal and enter the following command:

```ssh -i .ssh/do-key arch@your-droplets-ip-address```

For this command, replace "do-key" with the name of your key and replace "your-droplets-ip-address" with the ip address you just copied. 

Once here, you have successfully created an SSH key pair and connected it to a droplet. 

## Step 4: Using Cloud-init Configuration
A cloud-init configuration file is a way for us to automate the previous steps so that users can set up their ssh keys to their droplet in an automated fashion.

We will setup a cloud-init configuration file to automate the prior steps for easy future setups.

### Confirm that cloud-init is running on your server
- First, access your arch linux server with the following command in your terminal: 

```ssh arch```

- Once here, execute the following code to confirm that cloud-init is running on your server:

```systemctl status cloud-init```

From here you will see a large return, next to Active: your status should show as Active.

### Install Neovim onto your server
To execute this cloud-init file, we will need to install neovim onto our server to store the file that contains our commands to initialize.

- Initialize a package manager. For arch linux, we will be using pacman with sudo privileges to execute this. We will execute this with the following command:

```sudo pacman -Syu```

You will be prompted for a yes/no option (Y/n) to confirm the installation, type **Y**.
 
- Now, execute the following command to install neovim:

```sudo pacman -S neovim```

You will be prompted for a yes/no option (Y/n) to confirm the installation, type **Y**.

### Creating a yaml file for the initialization
- Once you have neovim installed onto your server, execute the following command to create a new yaml file:

```nvim cloud-init-config.yaml```

- In the yaml file we want to input syntax that other users can execute to configure their accounts. You can use this example syntax for your users to fill out:

```yaml
users:
  - default
  - name: new_user 
    primary_group: users
    groups: wheel
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIB... 

packages:
  - neovim
  - git 

disable_root: true
```
For this example replace the following:

    - name: change this to the desired username

    - primary_group: change this to the name of the desired group

    - groups: for admin privileges, use wheel, for user, use users

    - ssh-authorized-keys: after ssh-ed25519, paste the public key you copied from the terminal

    - packages: enter desired packages here 

- Once here, press ESC, then type ```:wq``` and press enter/return to save the contents. 

- Once done, execute the following commands to apply the cloud-init configuration:

```sudo cloud-init init --local```

```sudo cloud-init modules --mode=config```

```sudo cloud-init modules --mode=final```

Once you have completed these steps, you will now have a running Arch Linux server with a cloud-init file to automate the configuration of new users.

## References

How to Automate Droplet Setup with cloud-init. DigitalOcean Documentation. (2022, August 14). https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/ 

McNinch, N. (2024a, September 10). 2420-notes/week-one.md. GitLab. https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-one.md 

McNinch, N. (2024b, September 16). 2420-notes/week-two.md · main · cit_2420 / 2420-notes-F24 · GITLAB. GitLab. https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-two.md

McNinch, N. (2024c, September 24). 2420-notes/week-three.md. GitLab. https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-three.md 

SSH Academy. (2023, February 9). What is SSH public key authentication?. PAM solutions, Key Management Systems, Secure File Transfers. https://www.ssh.com/academy/ssh/public-key-authentication#:~:text=The%20motivation%20for%20using%20public,long%20passwords%20can%20not%20offer. 
