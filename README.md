# Moderately secure recursive Pi-hole DNS server running on raspberry pi using Ansible, Docker and unbound.

The title claims "moderately secure" because the aim is to leverage ansible for automating the best-practices configuration changes that are recommended for a new raspberry pi OS install.

The system I use for this is a raspberry pi 3 B+, which is why I don't bother with a 64-bit OS. Please feel free to fork and make changes for your personal system if you like what you see but want to make changes.

Some of the decisions are specific to the fact that this system is intended to be ran on a raspberry pi that is doing nothing other than DNS.

For that reason, in combination with the best-practices mentioned, here's a short non-exhustive list of what will be happening:

1. Create a personal admin (capable of sudo) user account for administration that is not the default pi user.
1. Add ssh public key to ~/.ssh/authorized_keys
1. Update all relevant localization settings.
1. Disable wifi and bluetooth (these are not needed for this system)
1. Disable `pi` user login
1. Explicitly allow only the personal admin account to login via ssh
1. Disable password auth for ssh.

Additionally, there are some changes that are specific to my network setup. I will try to make sure that these are configurable and transparent to maximize re-usability, but will include fake-value examples for if you have a similar setup.

Those changes include but aren't limited to:

1. Adding virtual interfaces so that the DNS server is reachable from my separate vlans.
1. Setting a static IP on all ethernet interfaces for the pi.
1. Setting a hostname for the pi.
1. ... More to come later.


The ultimate aim is to leverage ansible for setting up a raspberry pi running pihole using docker, with unbound for recursive dns resolution.

https://docs.pi-hole.net/guides/dns/unbound/

Inspired by the ansible role found here in addition to the original forked repo: 

https://github.com/TWinsnes/galaxy-role-pihole/blob/master/tasks/main.yml

Also:
https://github.com/geerlingguy/ansible-role-docker

NOTE: This is a forked repo that is under active development / changes.
The original readme is left below for now. 

## The below is for reference from the original that was forked. This repo is not ready to be used. Proceed with running a strangers code at your own peril.

# ansible-pi

![](https://raw.github.com/motdotla/ansible-pi/master/ansible-pi.jpg)

Setup your Raspberry Pi as Pi-Hole DNS server, WITHOUT WIFI (Ethernet Only).

It is based on the [complete guide to setting up your raspberry pi without a keyboard and
mouse](http://sendgrid.com/blog/complete-guide-set-raspberry-pi-without-keyboard-mouse/) that goes
along with this repo.

## Requirements

- Flash a **clean** Raspbian Lite image on an SD card
  URL: https://www.raspberrypi.org/downloads/raspbian/
  Mac: Use [ApplePI-Baker](https://www.tweaking4all.com/software/macosx-software/macosx-apple-pi-baker/)
- Enable SSH on the Raspberry (it is disabled by default). You can:
    - use `raspi-config` to enable SSH
    - create a file `/boot/ssh`
- Find the IP address your router will give to the RPI
- You need Ansible installed on your local computer (not the Raspberry Pi)
- Install SSHPass:
  - Ubuntu: `sudo apt install sshpass`
  - MacOS: `brew install http://git.io/sshpass.rb`

## Installation

On your local computer, clone and setup this ansible playbook.

```
git clone https://github.com/stibbons/ansible-rpi-pihole.git
cd ansible-rpi-pihole
cp hosts.example hosts
```

Edit the `hosts` file and set the IP address of your RPI (get this from the router or from the
command line).

Deploy using [ansible](http://www.ansible.com) (install instructions for ansible are in [requirements](#requirements) below).

```
./playbook.yml
```

Or:
```
ansible-playbook playbook.yml -i hosts --ask-pass --become -c paramiko
```

## Installing Ansible on your computer


### On Mac with Homebrew

```
brew install ansible
```

### On Mac without Homebrew

```
cd /tmp
git clone git://github.com/ansible/ansible.git
cd ./ansible
git checkout v1.4.3
sudo make install
sudo easy_install jinja2
sudo easy_install pyyaml
sudo easy_install paramiko
```

## History

This project was originally built when trying out my first Raspberry Pi. The setup process was not
as easy as I wanted.
