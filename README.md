# DevStackSetup
Set of scripts to setup an OpenStack DevStack service in a VM.

## Source and Credits
The scripts were basically taken from Youtube tutorial about [setting up DevStack](https://www.youtube.com/watch?v=bh2CHlxyIvw) (Video is in Portuguese) and the [DevStack setup page](https://docs.openstack.org/devstack/latest/)

## Instructions
Make sure to have [Vagrant](https://www.vagrantup.com/downloads) and [VirtualBox](https://www.virtualbox.org/) installed.
In the root directory for the project, run:
```bash
vagrant up
```
Vagrant will create an Ubuntu 20.04 instance machine that will host the DevStack.
After the machine has been created, ssh into it with `vagrant ssh`.
Switch to the stack user with `sudo su stack` and navigate to the /opt/stack/devstack directory.
Now run the "stack.sh" script.
Make sure to check the Troubleshoot sesssion for any issues.

## Troubleshoot
I had to make a few changes to the scripts and setup as I hit issues not shown in the video.

### Git Clone Timeout
During stack.sh execution, the "git clone" stage would time out.
To solve this issue I edited the file "stackrc" in the devstack folder, switching the line
```bash
GIT_BASE=${GIT_BASE:-https://opendev.org}
```
to
```bash
GIT_BASE=${GIT_BASE:-http://opendev.org}
```
### Unable to uninstall simplejson
This problem was raised during the pip install dependencies of stack.sh.
Editing the "inc/python" script, line 192 `$sudo_pip` to contain `--ignore-installed` at the end fixed the issue.

### Vagrant unable to mount VirtualBox shared folders
This is specific to VirtualBox 6.1 version, it seems. When running the `vagrant up` command, the VM setup would crash with a message
```
/sbin/mount.vboxsf: mounting failed with the error: Invalid argument
```
This seem to be related to the Vagrant Focal Fossa image.
The fix was taken from [here](https://www.mail-archive.com/ubuntu-bugs@lists.ubuntu.com/msg5944668.html)
Summarizing the steps:
- Halt the VM with `vagrant halt`;
- Go to VirtualBox GUI and add Guest Additions CD image as IDE unity (download the iso if needed from https://download.virtualbox.org/virtualbox/);
- Reload the machine with `vagrant up`;
- SSH into the machine with `vagrant ssh`;
- Run the following:
```bash
sudo apt-get remove virtualbox-guest-utils
sudo apt update
sudo apt upgrade
sudo apt-get install -y dkms build-essential
sudo mount /dev/cdrom /mnt
sudo /mnt/VBoxLinuxAdditions.run
exit
```
- Back on the Host machine: `vagrant reload`;

