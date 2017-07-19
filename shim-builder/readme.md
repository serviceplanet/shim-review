General
=======

This repository contains the scripts used to setup the build environment for building the unsigned Service Planet shim. The intention is to show that the unsigned Service Planet shim is build from the official shim 0.9 release with only the Service Planet signing key added.

We will use the vanilla CentOS 7.3 / 1611 (`CentOS-7-x86_64-Everything-1611.iso`) ISO image to build a qemu qcow2 VM image with packer in which we will build the shim. Packer is provided as a convenience but you can also manually install a VM in a virtualization solution of your choosing (VirtualBox, VMWare, etc.) with the `CentOS-7-x86_64-Everything-1611.iso` image. 

Using the `CentOS-7-x86_64-Everything-1611.iso` ISO as base allows us to create a reproducable build environment. This guarantees the use of the exact same buildchain Service Planet used to build the shim binary. In turn using the exact same buildchain will produce the exact same binary. You must **never** install any updates to this VM otherwise the reproducability of the build will probably be jeopardized.

Requirements
============

This guide assumes:

* You use an RedHat based Linux distribution like RHEL, CentOS or Fedora. This guide was written and tested with a fully updated CentOS 7.3 installation.
* You have the Service Planet shim build system [GIT repository]( https://github.com/serviceplanet/shim-builder) cloned at a location of your convienence. We will reference this location as `$PROJECT_HOME` in this guide. It is advisable to configure `PROJECT_HOME` as an environmental variable in your shell.
* You have imported and verified trust of the following identities / PGP keys:
 * **0xeed266b70f4fef10** Peter Jones (RedHat) 
 * **0x9ef4ec108bef11c5** Jasper Siepkes (Service Planet) 

Depedencies
-----------

**Installing RPM depedencies:**

```
$ sudo yum install qemu qemu-kvm libguestfs-tools-c git gnupg2
```

* **qemu**, **qemu-kvm** and **libguestfs-tools-c** In this guide we use Qemu (with KVM support) for building the shim in a VM. If you decide to use your own virtualization solution you don't need to install them.
* **git** and **gnupg2** GIT and GnuPG 2 are needed for cloning source repositories and veryfing them.

**non RPM depedencies:**

* **Packer** Only used if you want to Packer to build your VM instead of doing it manually. Can be downloaded from the Packer website[1]. Packer is a single file executable which can "just be run". These instructions were tested with Packer 0.12.3 for Linux 64bit On CentOS 7.3.

Verification
============

Below are instructions on how to verify that the artifacts used to build shim are unmodified.

Verify authenticity of the CentOS 7 image
-----------------------------------------

Packer will verify the checksum of the ISO image it will use for installation of the VM. Verify in Packers configuration `$PROJECT_HOME/packer/create_shim_builder_image.json` that the ISO used is `CentOS-7-x86_64-Everything-1611.iso` and the value for `iso_checksum` is correct. 

The correct SHA256 checksum for the ISO image can be obtained from the CentOS release notes. See https://wiki.centos.org/Manuals/ReleaseNotes for version 7 (1611).  

This ensures Packer creates an unmodified CentOS 7 (1611) VM.

Verify the Service Planet EV Code Signing certificate
-----------------------------------------------------

The Service Planet EV Code signing certificate is located in `keys/sp_code_signing_cert.der` and is signed by Global Sign. Verifying that the certificate was actually signed by Global Sign to be used as an EV Code signing certificate can be done in the steps described below.

First obtain the "GlobalSign Extended Validation CodeSigning CA - SHA256 - G3" (intermediate CA) certificate and the "GlobalSign Root CA - R3" (Root CA). After fetching them convert them PEM because openssl verify does not support DER and concatenate them to a single PEM file. These can be obtained from the GlobalSign website.

```
$ cd keys
$ wget https://secure.globalsign.com/cacert/gsextendcodesignsha2g3ocsp.crt
$ wget https://secure.globalsign.net/cacert/Root-R3.crt
$ openssl x509 -inform DER -outform PEM -text -in gsextendcodesignsha2g3ocsp.crt -out gsextendcodesignsha2g3ocsp.pem
$ openssl x509 -inform DER -outform PEM -text -in Root-R3.crt -out Root-R3.pem
$ cat Root-R3.pem gsextendcodesignsha2g3ocsp.pem > globalsign.pem
```

Convert the Service Planet EV Code signing certificate to PEM format:

```
$ openssl x509 -inform DER -outform PEM -text -in sp_code_signing_cert.der -out sp_code_signing_cert.pem
``` 

Now we can use OpenSSL to verify the Service Planet EV Code signing certificate was really signed by Global Sign:

```
$ openssl verify -CAfile globalsign.pem sp_code_signing_cert.pem
```

The output must read: "sp_code_signing_cert.pem: OK".

This ensures the certificate which shim uses is really a EV Code signing certificate issued by Global Sign specifically for Service Planet.

Building the Shim
=================

Perpare the shim source
-----------------------

In $PROJECT_HOME we will prepare the source tree which we are going to build later on in the build VM.


Check out the shim source and switch to the `0.9` release:
```
$ cd $PROJECT_HOME/shim
$ git clone https://github.com/rhinstaller/shim.git source
$ cd source
$ git checkout 0.9
```

Verify the authenticity of the `0.9` sources. `gpg` must indicate `Good signature` (this assumes you have established trust with the correct GPG keys as instructed in the `Requirements` chapter):
```
$ git tag -v 0.9
```

In order to make shim build on RHEL / CentOS 7.3 two patches need to be applied to the Makefile:

```
$ git cherry-pick d9a4c912c0aa72905ca793b555dcb0afb33e3b30
$ git cherry-pick 937503156b73626b0efa88913673d9f452d1f579
```

Verify with GIT diff that the only thing that was modified was the Makefile:

```
$ git diff 0.9
```

Copy the Service Planet signing cerficate in to the shim source so we can embed it in to shim when we are going to build it:

```
$ cp $PROJECT_HOME/keys/sp_code_signing_cert.der $PROJECT_HOME/shim/source/
```

Building a VM image for building
--------------------------------

For your convience a Packer config file is included to build a VM image in which we will build the shim. 

Alternativly you can also install CentOS 7 1611 yourself in a VM. Don't install any updates in the VM otherwise you don't get a reproducable build! Use the vanilla `CentOS-7-x86_64-Everything-1611.iso`. Take a look at the the Anaconda Kickstart script `$PROJECT_HOME/packer/http/centosks.cfg` to see which packages need to be installed when you go the manual way. Also the folder `$PROJECT_HOME/shim/source` needs to be copied in to the VM.

Use Packer to create the KVM (qcow2) image in which we will later build the shim:

```
$ cd ${PROJECT_HOME}/packer
$ packer build create_shim_builder_image.json
```

The resulting image will be in the `$PROJECT_HOME/packer/packer_output/` directory.

Starting the build VM
---------------------

In the following part we will start the VM image which we have just created with Packer. 

If you chose the manually install a VM (without using Packer) you must skip this step. However you do need to manually copy `$PROJECT_HOME` in to your VM.

```
$ qemu-system-x86_64 -m 2048 -drive file=$PROJECT_HOME/packer/packer_output/shim_builder_image-packer,if=virtio -enable-kvm -net none
```

Used qemu flags:
* `-m 2048` Give the VM 2GB of RAM.
* `-drive file=$PROJECT_HOME/packer_output/shim_builder_image-packer,if=virtio` Attach the cqow2 image we created with packer as a drive. `if=virtio` specifies we want to use the VirtIO drivers.
* `-enable-kvm` Enable KVM for hardware acceleration.
* `-net none` Disable all networking.

Building the shim in the build VM
---------------------------------

Login in to the VM with `root` / `root`.

In your VM build the shim with the Service Planet certificate in them:

```
# cd /root/source
# make VENDOR_CERT_FILE=/root/source/sp_code_signing_cert.der
```

This produces the following deliverables:

* `shimx64.efi`
* `mmx64.efi.signed`
* `fbx64.efi.signed`

Copy the deliverables out of your VM. Do this by stopping your VM and then running the following command in on your host PC:

```
$ virt-copy-out -a $PROJECT_HOME/packer/packer_output/shim_builder_image-packer /root/source/shimx64.efi /root/source/mmx64.efi.signed /root/source/fbx64.efi.signed $PROJECT_HOME/shim/
```

The compiled unigned Service Planet shim will be located in: `$PROJECT_HOME/shim/`.

High level overview of shim
===========================

Assuming secure boot is turned on:

* Shim will load grub into memory.
* Shim checks if MokDB haven't been modified.
* Shim checks if the binary isn't blacklisted
* Shim checks if the binary is whitelisted.
* Shim checks the binary against the shim build key.
* Shim checks the binary against the shim built-in key.

If the binary has been verified and authorized to run, the shim will start executing grub2 via an entry point with 2 variables. This is executed to the UEFI calling convention (MS x64).

