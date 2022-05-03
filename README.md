Objective
=========

I have re-edited my workspace to create ISO images for kitting TX120s7 and Thinkpad x220/x230 for public use.
This project originally used the preseed system of debian, but now it only supports AutoInstall for Ubuntu 22.04.

References
==========

In 22.04, the EFI boot image (boot/grub/efi.img) is no longer included in the ISO image.
Please refer to the following document for more details and a workaround

* https://askubuntu.com/questions/1403546/ubuntu-22-04-build-iso-both-mbr-and-efi

Instructions for use
====================

To download the ISO image and populate the initial files, the following tasks are performed only once.

    $ make download
    $ make init

The following tasks must be performed each time an ISO file is generated;

    $ make setup
    $ env LANG=C make geniso

In Ubuntu 22.04, the geniso task might be failed due to the locale, so it is safe to specify LANG=C.

user-data file
--------------

Customization of the installation process focuses on config/user-data.

* config/user-data.efi - Configuration to create ESP area for UEFI boot
* config/user-data.mbr - Configuration for MBR (BIOS) boot

Please place the prefered configuration file to the config/user-data.

The following is a summary of the major settings.

Default user password
---------------------

Change the username: and password: lines in user-data as desired.

* ID: ubuntu
* Password: secret

The hash value specified in the password: line was generated by the command,

    $ openssl passwd -6 -salt "$(openssl rand -hex 8)" secret

Other settings
==============

SSH keys
--------

Following is an example for providing ssh keys to the default user, ubuntu.

    ssh:
      authorized-keys:
        - "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAAIH8mvfUPhRddvGXBxGcvwo5m3CRVOf8RbFXwaUa9mhLX comment"
        - "..."

Support for APU/APU2
--------------------

The following configurations are made to work with devices that only have a serial terminal.

* Add ``console=ttyS0,115200n8`` to the end of the "append" line in config/isolinux/txt.cfg
* Add ``console=ttyS0,115200n8`` to the end of the "linux" line in config/boot/grub/grub.cfg.

APU/APU2 do not support EFI boot, so please copy config/user-data.mbr to config/user-data.
