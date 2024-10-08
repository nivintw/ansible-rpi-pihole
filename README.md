# Best-effort best-practices recursive Pi-hole DNS server running on raspberry pi using Ansible, Docker and unbound

TODO: Update this README.md

TODO: Add a secondary role for installing / configuring my fork of doddns for managing the dynamic dns configuration/updates.

This project aims to:

1. Lower the barrier of entry to configuring and deploying [pihole](https://pi-hole.net/) on a Raspberry Pi running [Ubunutu 24.04 Server LTS](https://ubuntu.com/download/server) with widely-accepted security configurations.
1. Do so while also incorporating current (2024) best-practices for managing and deploying software stacks.

In case you have a background in this area and want to see the tech stack here it is:

1. [Ansible Community](https://docs.ansible.com/ansible/latest/community/index.html), including ansible-vault for secrets
1. OCI container management via [Podman](https://podman.io/)
1. OS and SSH Hardening via https://github.com/dev-sec/ansible-collection-hardening
1. [Ubunutu 24.04 Server LTS](https://ubuntu.com/download/server)
1. Public Key Infrastructure
1. [Pi-hole ad blocking](https://pi-hole.net/) via the [Pihole OCI container](https://hub.docker.com/r/pihole/pihole)
1. [Unbound recursive DNS](https://docs.pi-hole.net/guides/dns/unbound/)
1. ~~Multi-vlan support via `vlan` package~~
   - Temporarily disabled due to pending updates.

Likewise if you want to see some of the key changes I try ensure happen with this project:

1. Change ubuntu user default password
1. Ensure only PKI-based authentication for SSH
1. Add user-specified admin account for the pi
1. Set localization options: keyboard, wifi country, locale
1. Update and safe upgrade apt packages since OS release
1. Install useful utilities: `htop`, `vim`, etc
1. Enable HDMI hotplug with reasonable defaults
   - You will be able to plug in a monitor after the pi has booted!
1. Install Pi-hole in a docker container with unbound set for the recursive upstream DNS provider
1. ~~Set static IPv4 address for the pi host~~
   - This is handled by the DHCP server on my local network being configured to assign this device a static ip.
1. Add pi host to multiple vlans (optional and dependent on your local network configuration)
1. Set user-defined password for pihole web admin interface
1. General ssh hardening as provided by [ansible-ssh-hardening](https://github.com/dev-sec/ansible-collection-hardening)
1. Disable wifi and bluetooth
   - These are not needed for this system, you can change as you see fit for yours

## Getting Started

The vast majority of the configuration will be handled by Ansible.
Doing all of this configuration manually (like I was before working on this project) is complex, error-prone, and steps are likely to be forgotten in the time (months or years) between having to do this configuration again.

That being said, there are a few steps that need to happen outside of ansible first.

1. Install [Ubunutu 24.04 Server LTS](https://ubuntu.com/download/server) onto an SD card.
   - The recommended approach is to use [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to install and configure Ubuntu.
   - Enable SSH and configure a personal admin user during installation.
   - I recommend adding a relevant public key for your personal admin user during installation/ssh configuration before the first boot, but this is not technically required.
     - Doing this requires an SSH keypair
     - Either use an existing keypair if you have one, or create a new one.
     - See [Creating SSH Keys](https://confluence.atlassian.com/bitbucketserver/creating-ssh-keys-776639788.html) for a write-up that I personally appreciate.
1. Install the SD card and boot the pi with an ethernet connection
1. Install Ansible onto the computer you will be using for running the playbooks.

Additionally, there are some changes that are specific to my network setup. I will try to make sure that these are configurable and transparent to maximize re-usability, but will include fake-value examples for if you have a similar setup.

Those changes include but aren't limited to:

1. ~~Adding virtual interfaces so that the DNS server is reachable from my separate vlans.~~
1. ~~Setting a static IP for all ethernet interfaces (physical and virtual) for the pi.~~

NOTE: The above changes are currently disabled in the playbooks while I figure out if I actually need to still do these things on the pihole host.
Currently, I configure my DHCP server on my local network to assign the pihole host device a static IP address, so having the device set one is no longer needed. This approach (assigning a static ip via DHCP vs on the host) is especially recommended / important if your pi device travels between networks; a host-based static IP assignment/request can result in the device being unable to obtain an IP address if the static IP is not available. E.g. because it's assigned to another device already or the network configuration doesn't match the host-assigned static IP address.

This project ended up being more of a lift than I expected at first, but would not have been successful without the open-source community.

Inspired by the ansible role found here in addition to the original forked repo:

https://github.com/TWinsnes/galaxy-role-pihole/blob/master/tasks/main.yml

Also:
https://github.com/geerlingguy/ansible-role-docker

Also:
https://github.com/emielmolenaar/ansible-role-docker-pihole

Also:
https://github.com/dev-sec/ansible-collection-hardening

I don't know how much actually remains of the original fork, but I wanted to try to give credit everywhere it is due. If you find references or code that looks like another project it almost certainly was at least inspired by it. Omission of a reference is strictly accidental and I will happily add anyone i've forgotten.

## Installing Ansible on your computer

As mentioned above, much of the configuration is managed via [ansible](https://docs.ansible.com/).
This section includes instructions to install Ansible on your local machine if you do not already have it installed.

### Using pipx - Recommended Approach

Using pipx to manage python-based command line utilities (like ansible!) is highly recommended.
In short, it allows you to install and run python applications in isolated environments. See [the pipx official site](https://pipx.pypa.io/stable/) for more information.

Full instructions for installing ansible with pipx can be found on the ansibile documentation site [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible-with-pipx).

Alternatively, use the below to just install the full Ansible package if you want to skip the external docs.

```sh
pipx install --include-deps ansible
```

### Other Install Options

For alternative install options for ansible, see the official documentation available [here](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).
Alternative options include using pip, OCI containers, and development install options.
For most people, the `pipx` based installation will be what you want.

## Playbook Installation (I.e. This Repository)

On your local computer, clone and setup this ansible playbook.

```
git clone https://github.com/nivintw/ansible-rpi-pihole
cd ansible-rpi-pihole
cp inventory.yaml.example inventory.yaml
```

Edit the `inventory.yaml` file and set the IP address of your RPI (get this from the router or from the
command line).

Deploy using [ansible](http://www.ansible.com).

### WARNING: The instructions below this point have not been updated.

The playbooks and code _do_ work, but these instructions may not.
I plan on updating the readme soon, but please proceed with caution.
If you are familiar with Ansible and the other tools, I hope the code base is relatively easy to understand and follows expectations as much as possible.
If you are not familiar with ansible, maybe wait until I get a chance to update these instructions.
There are some variables that you'll want to override for your setup, as well as leveraging ansible-vault and the need to create a vault file.

```
./playbook.yml
```

Or:

```
ansible-playbook playbook.yml -i hosts --ask-pass --become -c paramiko
```
