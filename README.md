This repo is for review of requests for signing shim.  To create a request for review:

- [x] clone this repo
- [x] edit the template below
- [x] add the shim.efi to be signed
- [x] add build logs
- [x] commit all of that
- [ ] tag it with a tag of the form "myorg-shim-arch-YYYYMMDD"
- [x] push that to github
- [ ] file an issue at https://github.com/rhboot/shim-review/issues with a link to your tag

Note that we really only have experience with using grub2 on Linux, so asking
us to endorse anything else for signing is going to require some convincing on
your part.

Here's the template:

-------------------------------------------------------------------------------
What organization or people are asking to have this signed:
-------------------------------------------------------------------------------
`Service Planet Rotterdam B.V.`

-------------------------------------------------------------------------------
What product or service is this for:
-------------------------------------------------------------------------------

``` no-highlight
The product for which we are applying for shim signing is an appliance codenamed
"Cyric" which is designed to help support personnel in stores. Cyric consists of
a server and a client. The Cyric client is a Linux distribution which users can
boot via PXE or USB on any PC. Once the Linux distribution is booted the user is
launched into a special application and presented with a menu of diagnostic and
options interesting for store customer support personnel.
```

-------------------------------------------------------------------------------
What's the justification that this really does need to be signed for the whole world to be able to boot it:
-------------------------------------------------------------------------------

``` no-highlight
Cyric deals with a very large amount of different models, brands and types of
consumer PC's that are not under our direct control or ownership. Unfortunately
because we deal with a vast number of different types of consumer notebooks and
desktops we encounter all kinds of problems with UEFI's. Some of them don't have
the ability to turn of secure boot even though that ability was mandated for
getting the Windows sticker on the PC at that time. Some UEFI's are so
complicated or poorly designed that even support personel (which operate Cyric)
have trouble finding the off switch. Some machines interpret disabling secure
boot as "enable BIOS compatibility" and can no longer boot via UEFI. Also
Microsoft has dropped the requirement for UEFI implementations that they have to
provide the ability to disable secure boot. So besides that we would like to
offer our users a more seamless experience (not having to manually disable
secure boot and then not forgetting to re-enable it) we encounter a lot of
situations in which we need a signed bootloader. We also anticipate that the
need for being "supported by default" (ie. a signed shim) will only grow now
that Microsoft has dropped the requirement to disable secure boot.
```

-------------------------------------------------------------------------------
Who is the primary contact for security updates, etc.
-------------------------------------------------------------------------------
- Name: Jasper Siepkes
- Position: ICT Manager (Co-founder), Developer
- Email address: siepkes@serviceplanet.nl
- PGP key, signed by the other security contacts, and preferably also with signatures that are reasonably well known in the linux community:
PGP Key ID: 0x9EF4EC108BEF11C5
PGP Key Fingerprint: A15F E099 A0D2 5523 7CF5  5E16 9EF4 EC10 8BEF 11C5

-------------------------------------------------------------------------------
Who is the secondary contact for security updates, etc.
-------------------------------------------------------------------------------
- Name: Wai Kon Tse
- Position: Developer
- Email address: tse@serviceplanet.nl
- PGP key, signed by the other security contacts, and preferably also with signatures that are reasonably well known in the linux community:
PGP Key ID: 0xD20A5F35772B17D4
PGP Key Fingerprint: 4932 5B19 1DB2 26F9 3F0E 56B6 D20A 5F35 772B 17D4

-------------------------------------------------------------------------------
What upstream shim tag is this starting from:
-------------------------------------------------------------------------------
`https://github.com/rhboot/shim/releases/tag/12`

-------------------------------------------------------------------------------
URL for a repo that contains the exact code which was built to get this binary:
-------------------------------------------------------------------------------
https://github.com/serviceplanet/shim-builder

-------------------------------------------------------------------------------
What patches are being applied and why:
-------------------------------------------------------------------------------
`There are no patches being applied.`

-------------------------------------------------------------------------------
What OS and toolchain must we use to reproduce this build?  Include where to find it, etc.  We're going to try to reproduce your build as close as possible to verify that it's really a build of the source tree you tell us it is, so these need to be fairly thorough. At the very least include the specific versions of gcc, binutils, and gnu-efi which were used, and where to find those binaries.
-------------------------------------------------------------------------------

``` no-highlight
We are using CentOS 7.5 and the bundled gcc. Everything can be reproduced by
following the guide provided by the url **https://github.com/serviceplanet/shim-builder**
```

-------------------------------------------------------------------------------
Which files in this repo are the logs for your build?   This should include logs for creating the buildroots, applying patches, doing the build, creating the archives, etc.
-------------------------------------------------------------------------------
build_log


-------------------------------------------------------------------------------
Put info about what bootloader you're using, including which patches it includes to enforce Secure Boot here:
-------------------------------------------------------------------------------

``` no-highlight
We are using grub2 bootleader provided by CentOS 7.5.1804. Grub2 provided by
CentOS 7.5.1804 contains secure-boot patches.
The srpm of CentOS 7.5.1804 grub2 is located at http://vault.centos.org/7.5.1804/os/Source/SPackages/grub2-2.02-0.65.el7.centos.2.src.rpm
```

-------------------------------------------------------------------------------
Put info about what kernel you're using, including which patches it includes to enforce Secure Boot here:
-------------------------------------------------------------------------------

``` no-highlight
The kernel we are using is fedora 28 kernel tagged **kernel-4.16.14-300.fc28**
with commit hash of **217860d07996562757ba22a1108c218c5a40b38e**.
```

--------------------------------------------------------------------------------
sha256 of shimx64.efi
--------------------------------------------------------------------------------

``` no-highlight
b52df1bd99a29cd4e6f53c799fc6c2c89650925c8f073c8fdd1b21fcf985bd02  shimx64.efi
```

--------------------------------------------------------------------------------
Microsoft Uefi submission ID
--------------------------------------------------------------------------------

``` no-highlight
14105650530628646
```
