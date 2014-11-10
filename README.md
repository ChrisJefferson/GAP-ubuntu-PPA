## GAP Ubuntu PPA

This file provides instructions for creating a Debian .deb archive
installing [GAP](http://www.gap-system.org/) to /opt, which then can be uploaded to an
[Ubuntu PPA](https://help.launchpad.net/Packaging/PPA).

In particular, it describe how I create the packages in my personal GAP PPA:

  https://launchpad.net/~mhorn/+archive/ubuntu/gap

## Step-by-step instructions


### Download the sources

Running the `fetch-gap` script takes care of the following steps:

1. Download the GAP package archive you want to package, usually the latest,
   which currently is
   
     `ftp://ftp.gap-system.org/pub/gap/gap47/tar.bz2/gap4r7p5_2014_05_24-20_02.tar.bz2`

2. Pick a name for the final package. Since `gap` is already taken by
   the official GAP package for Debian / Ubuntu, we must use something
   else. I chose the (rather unimaginative) name `gap-for-opt`.
   
3. Rename the source tarball to the format Debian expects

      mv gap4r7p5_2014_05_24-20_02.tar.bz2 gap-for-opt_4.7.5+20140524.orig.tar.bz2

### TODO

* describe creating Ubuntu Launchpad.net account for PPA
* * https://help.launchpad.net/YourAccount/NewAccount

* describe how to create / upload a GnuPG key for use with that, and also SSH key(s)
* * https://help.launchpad.net/YourAccount/ImportingYourPGPKey
* * https://help.launchpad.net/YourAccount/CreatingAnSSHKeyPair

* describe Vagrant setup;
   also state which packages need to be installed
   (Of course if you are using Ubuntu anyway, you don't need to
   setup a VM)

* Read these instructions:
* * https://help.launchpad.net/Packaging/PPA
* * https://help.launchpad.net/Packaging/PPA/Uploading

* ... copy PPA GPG key into VM...

* The following steps can be taken in the VM (or on a regular Ubuntu 
system) to build and upload the source .deb

```
    # Set personal infromation
    export DEBFULLNAME="Max Horn"
    export DEBEMAIL="max@quendi.de"

    # Change into the extracted source dir (into which we put the debian/ dir)
    cd /vagrant/gap-for-opt-4.7.5+20140522/
    
    # Make a new revision and update the changelog file
    dch -i
    
    # Build the source tarball
    debuild -S -sa

    # Finally, upload it. Make sure to use the right PPA name here;
    # and also the file name must be adapted (in particular, the revision
    # string, i.e. "ubuntu4").
    # Moreover, the upload requires that you setup a GnuPG key as
    # described above
    dput ppa:mhorn/gap gap-for-opt_4.7.5+20140522-1ubuntu4_source.changes
```
