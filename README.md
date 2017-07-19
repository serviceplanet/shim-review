# shim-review

According to the shim signing procedure: https://pjones.fedorapeople.org/shim-signing-procedure.html

# Who, what and why

We (me, Jasper Siepkes and my colleague Wai Kon Tse) represent an organization
called Service Planet Rotterdam B.V.. We are located in Rotterdam, the
Netherlands and specialize in consumer electronics after sales services; In
short that means we help our customers help their customers. This ranges from
the repair of PC's and onsite installation to helpdesk services and appliances to help support personnel in stores.
The product for which we are applying for shim signing is an appliance codenamed
"Cyric" which is designed to help support personnel in stores. Cyric consists of
a server and a client. The Cyric client is a Linux distribution which users can
boot via PXE or USB on any PC. Once the Linux distribution is booted the user is
launched into a special application and presented with a menu of diagnostic and
options interesting for store customer support personnel. Some examples of
features it currently contains:

* Datavault: Transfer (backup) data from the customers PC in to a datavault on
the Cyric server. By creating a fingerprint of the customers PC and linking it
to the datavault we prevent accidental transfer of customer data to another
customers PC.
* Hardware testing: Performs a number of hardware tests like checking for memory
defects, CPU benchmarking, HDD SMART and DST tests, etc.
* Image creation and restoration: Allows the cloning of HDD's and restoring to
other HDD's. Useful for preloading large amounts of machines.
* Data destruction: Wipe HDD's. Useful for ensuring destruction of customer data
when the customer returns a machine for a credit.
* etc.

Cyric is currently in use in the Netherlands and Belgium in all stores of the
Media Markt and Staples. We are currently also working working with HP on
integrating it in their DAO procedure.

For the product itself we use a custom kernel because we need to backport
support for the latest and greatest drivers. We are also experimenting with
creating fixes for weird hardware (still in the early phases). 


# does it really need to be signed for the whole world to be able to boot it


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


# Security contacts

a list of at least two security contacts and for each one: their name, their
position that leads them to be on this list, an email address a PGP key, signed
by the other security contacts, and preferably also with signatures that are
reasonably well known in the linux community.


## Security contacts:

Full name: Jasper Siepkes    
Phone: +31 6 52084933   
Phone: +31 10 4008207  
Company: Serivce Planet Rotterdam B.V.  
Position: ICT Manager (Co-founder), Developer  
Location: the Netherlands, Rotterdam  
E-Mail (primary): siepkes@serviceplanet.nl  
E-Mail (backup): jasper@siepkes.nl
PGP Key ID: 0x9EF4EC108BEF11C5  
PGP Key Fingerprint: A15F E099 A0D2 5523 7CF5  5E16 9EF4 EC10 8BEF 11C5  
Fedora Key server: https://keys.fedoraproject.org/pks/lookup?search=0x9EF4EC108BEF11C5&op=vindex  
Full public key (including photo for ID): https://www.serviceplanet.nl/pgp/0x9EF4EC108BEF11C5.asc  
Facebook (added my PGP key): https://www.facebook.com/jasper.siepkes  
Github (added my PGP key): https://github.com/siepkes


Full name: Wai Kon Tse  
Company: Serivce Planet Rotterdam B.V.  
Position: Developer  
Location: the Netherlands, Rotterdam  
E-Mail (primary): tse@serviceplanet.nl  
PGP Key ID: 0xD20A5F35772B17D4  
PGP Key Fingerprint: 4932 5B19 1DB2 26F9 3F0E 56B6 D20A 5F35 772B 17D4
Fedora Key server:  
https://keys.fedoraproject.org/pks/lookup?search=0xD20A5F35772B17D4&op=vindex


As for verification of my identity (Jasper); I'm not close enough with any
prominent Linux developer for them to verify my identity. I'm also more active
in the Java, SmartOS (Illumos / Solaris) and Hashicorp related communities.
Internally we use PKI for authentication / verification purposes. We recognize
the value of PGP in this scenario and have therefor accelerated roll out of PGP
for our developers. Luckily for us our hardware tokens support the OpenPGP spec
so that made things easier for us. 

Ways of veryfing my identity:

* I've uploaded my GPG key to the Service Planet domain:
https://www.serviceplanet.nl/pgp/0x9EF4EC108BEF11C5.asc . You can verify the
domain name is owned by Service Planet Rotterdam B.V. by checking the .nl
registrar, SIDN: https://www.sidn.nl/?language_id=2
** I've also added my key to the DNS: dig siepkes._pka.serviceplanet.nl TXT
@8.8.8.8
* My GPG key is in my FaceBook profile: https://www.facebook.com/jasper.siepkes
* My GPG key is in my GitHub profile: https://github.com/siepkes
** It can be obtained with the following command: curl -H "Accept:
application/vnd.github.cryptographer-preview"
https://api.github.com/users/siepkes/gpg_keys
* I can provide a digitally signed PDF extract from the Dutch chamber of
commerce about Service Planet.


# the shimx64.efi binary we want signed

* shimx64.efi.tar.bz2 [2]
* shimx64.efi.tar.bz2.asc (Signature created with 0xD20A5F35772B17D4)

# Which shim version

We used the official 1.2 release from GitHub [1].

# Build instructions

In order to make verification of the shim binary easier we build the shim with a
vanilla CentOS 7.1611 ISO image in a VM. That also make it easier for ourselves
to keep an archivable (frozen in time) version of the build environment.
Please see the readme.md in the shim-builder folder for instructions. There is a Packer
config to easily create the VM image or you can manually create the VM.

# Microsoft shim submission number

UEFI submission #1960972

# Submitted signed cab file

The cab file which has been sent to Microsoft as UEFI submission is shimx64.efi.cab.
The cab file has been signed with our Service Planet EV code signing key.

[1] https://github.com/rhboot/shim/releases/download/12/shim-12.tar.bz2  
[2] shimx64.efi.tar.bz2 SHA512: ad19aa29156b327291233e78a579827fd6f3d328e03dff259dd25a691b9496fbf982e79b76969b013d4a4f0f8837f7b804566b2eb7b9052e3286b12c21604d2a
