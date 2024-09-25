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

#