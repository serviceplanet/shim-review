Make sure you have provided the following information:

 - [ ] link to your code branch cloned from rhboot/shim-review in the form user/repo@tag
 - [ ] completed README.md file with the necessary information
 - [ ] shim.efi to be signed
 - [ ] public portion of your certificate embedded in shim (the file passed to VENDOR_CERT_FILE)
 - [ ] any extra patches to shim via your own git tree or as files
 - [ ] any extra patches to grub via your own git tree or as files
 - [ ] build logs


###### What organization or people are asking to have this signed:
``` no-highlight
We (me, Jasper Siepkes and my colleague Wai Kon Tse) represent an organization
called Service Planet Rotterdam B.V.. We are located in Rotterdam, the
Netherlands and specialize in consumer electronics after sales services.
In short that means we help our customers help their customers. This ranges from
the repair of PC's and onsite installation to helpdesk services and appliances
to help support personnel in stores.
```

###### Version of shim:
`https://github.com/rhboot/shim/releases/tag/12`

###### Sysdev Submission ID:
`[your text here]`

###### What product or service is this for:

``` no-highlight
The product for which we are applying for shim signing is an appliance codenamed
"Cyric" which is designed to help support personnel in stores. Cyric consists of
a server and a client. The Cyric client is a Linux distribution which users can
boot via PXE or USB on any PC. Once the Linux distribution is booted the user is
launched into a special application and presented with a menu of diagnostic and
options interesting for store customer support personnel.
```

###### What's the justification that this really does need to be signed for the whole world to be able to boot it:

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
