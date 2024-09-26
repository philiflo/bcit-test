steps to write
- uploading digital ocean image
- create the ssh key pair and authenticate it in digital ocean
- add public key to digital ocean 
- create a droplet running arch linux using the DigitalOcean web console
- use a cloud init config file to:
    - create a new regular user
    - install some initial packages
    - add a public ssh key to the authorized_keys file in your new users home directory
    - disable root access via ssh

# Creating a remote server with DigitalOcean

## Preface
This guide will provide you with the steps needed to start up and run a remote server that is backed with a cloud service provider. For this scenario, we will be using DigitalOcean, which is a modular and extensive cloud service provider that is used by various businesses for their website usage.

## Step 1 - Downloading and Uploading the DigitalOcean Image

The first step is to download arch linux onto DigitalOcean. We will do this by downloading the Arch Linux image that corresponds to our use, and uploading it onto our DigitalOcean account.

### Downloading the image
- Follow this link on your internet browser: https://gitlab.archlinux.org/archlinux/arch-boxes/-/packages/1545
This will lead you to a gitlab page that contains the Arch Linux image you'll need to download

- From here, navigate to the most recent 'images' folder. You'll see a long list of Arch Linux images, for this tutorial we want to download the image that ends with the 
'.qcow2' extension (it will be approximately 496mb)

### Uploading the image
- Once you have your image downloaded, go back to your DigitalOcean homepage. Navigate to the 'Backups & Screenshots' option located on the sidebar. From here, navigate to the Custom Images tab.

- In the main module, click the Upload Image option and upload the Arch Linux image you've downloaded.

- Once here you will be given choices of different regions and a distributor to select. From here (assuming you are on the West Coast), select Arch Linux and select San Francisco.

## Step 2: Create the SSH Key Pair and Authenticate it
DigitalOcean utilizes a key pair system to authenticate users and connect clients and a remote machine (for this situation, a cloud service). 

This system is much more secure than the usual password system as key pairs contain a complex encrypted message, which serve as a better line of defense towards brute attacks.

### Create your SSH Key Pair
- Open your Terminal and enter one of the following commands:
If you are using the powershell -
ssh-keygen -t ed25519 -f C:\Users\your-user-name\.ssh\do-key -C "youremail@email.com"

If you are using a MacOS shell -
ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "your email address"

For both scenarios, replace 'your email' with the email you used to make your DigitalOcean account, and 'your-user-name' with your DigitalOcean username. If you would like to change the name of your droplet, you can change 'do-key' to your desired name. 

- From here, you will be prompted for a passphrase for security. Remember this passphrase as you will need it to access your key.

### Access your public key
- In the terminal, enter one of the following commands:
For Powershell users -
Get-Content C:\Users\your-user-name\.ssh\do-key.pub | Set-Clipboard

For MacOS users -
pbcopy < ~/.ssh/do-key.pub

Once entered, copy the text that is returned.

- Once here, go back to the DigitalOcean console and navigate to the 'Security' tab, it is located in the main module in the 'Settings' option located on the sidebar. From here, click the 'Add SSH Key' option.

- In the 'Public Key' textbox, enter the text you copied from the terminal. You are also prompted for a key name, enter a name that is appropriate for the servers use. 

It is important to distinguish this public key name from the other ones that may be connecting to this server. Make sure your name is unique to your use. 

## Step 3: Create a new Arch Linux droplet
*preface here*

### Make a new Arch Linux droplet
- In the DigitalOcean homepage, navigate to the 'Create' button at the top of your screen. From there, select the 'Droplet' option. 

- Once here, you will be prompted for various settings:
Choose Region - San Francisco
Datacenter - SF03
Choose an Image - Locate your downloaded Arch Linux image you downloaded from earlier, it will be under the 'Custom Image' tab
Choose Size - From here you can choose a droplet type for prefered optimization. If you are looking to save money with this droplet, you can select the 'basic' option. From there, select your prefered CPU option, the cheapest option under 'Regular Intel' should be adequate for regular use. 
Backups - Enable backups for more fault security
Choose Authentication Method - SSH Key, select the key pair you made from earlier
Finalize Details - From here, enter your prefered hostname. Under projects, select the project you wish to connect this droplet to.

### Connect to your Arch Linux droplet
- In the DigitalOcean homepage, navigated to the project you connected your droplet to. In the main line you will find a set of numbers resembling an IP address, copy this.

- Once copied, navigate back to the terminal and enter the following command:
ssh -i .ssh/do-key arch@your-droplets-ip-address

For this command, replace 'do-key' with the name of your key and replace 'your-droplets-ip-address' with the ip address you just copied. 

Once here, you have successfully created an SSH key pair and connected it to a droplet. 







