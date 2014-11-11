## GAP Ubuntu PPA

This file provides instructions for creating a Debian .deb archive
installing [GAP](http://www.gap-system.org/) to /opt, which then can be uploaded to an
[Ubuntu PPA](https://help.launchpad.net/Packaging/PPA).

In particular, it describe how I create the packages in my personal GAP PPA:

  https://launchpad.net/~mhorn/+archive/ubuntu/gap
  
The resulting package can be used with services like [Travis CI](https://travis-ci.org/)
in order to run test suites of GAP packages "in the cloud".

## Step-by-step instructions

### Setup a Ubuntu Launchpad account

TODO

Most what you need is described here:
* http://developer.ubuntu.com/publish/apps/other-forms-of-submitting-apps/ppa/

* create Ubuntu Launchpad.net account for PPA
* * https://help.launchpad.net/YourAccount/NewAccount

* create / upload a GnuPG key for use with that, and also SSH key(s)
* * https://help.launchpad.net/YourAccount/ImportingYourPGPKey
* * https://help.launchpad.net/YourAccount/CreatingAnSSHKeyPair

* Sign Code of conduct
* * https://help.launchpad.net/Signing%20the%20Ubuntu%20Code%20of%20Conduct


### Setup a machine with correct Ubuntu version

Currently, Travis is using Ubuntu 12.04 LTS ""Precise Pangolin" 64 bit,
[see here](http://docs.travis-ci.com/user/ci-environment/#CI-environment-OS).
The easiest way to build the require Debian source packages is on
a machine running exactly that. (Note that in principle, a more recent
Ubuntu version should also be OK).

The easiest way to do that is to run a virtual machine. For this, I used
[Vagrant](https://www.vagrantup.com/), as that makes it very easy
to setup and configure the VM.

After installing valgrind, run the following command to download a VM, and start the machine running:

    vagrant up
    
Any files you place the this directory will be accessable in the VM under the directory ````/valgrind````.
You can use this to move your .gnupg directory. You can log into the VM by running ````valgrind ssh````.

You will also need to ```apt-get install dput devscripts debhelper dh-make``` (this is done automatically by vagrant).

(Of course if you are using Ubuntu anyway, you don't need to setup a VM)


### Download the GAP sources

Running the `fetch-gap` script on your Ubuntu machine resp. (if you are using a VM)
in a directory accessible from the VM takes care of the following steps:

1. Download the GAP package archive you want to package, usually the latest,
   which currently is
   
     `ftp://ftp.gap-system.org/pub/gap/gap47/tar.bz2/gap4r7p5_2014_05_24-20_02.tar.bz2`

2. Pick a name for the final package. Since `gap` is already taken by
   the official GAP package for Debian / Ubuntu, we must use something
   else. I chose the (rather unimaginative) name `gap-for-opt`.
   
3. Rename the source tarball to the format Debian expects

    ```mv gap4r7p5_2014_05_24-20_02.tar.bz2 gap-for-opt_4.7.5+20140524.orig.tar.bz2```

### Prepare

* extract the `gap-for-opt_4.7.5+20140524.orig.tar.bz2 tarball`
* rename the extracted directory to `gap-for-opt-4.7.5+20140524`
  (note how `_4` changed to `-4`)
* copy or move the `debian` directory into the extracted directory


### Upload


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
    # string, i.e. "ubuntu5").
    # Moreover, the upload requires that you already setup your GnuPG key as
    # described above
    dput ppa:mhorn/gap gap-for-opt_4.7.5+20140522-1ubuntu5_source.changes
```

