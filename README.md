# centos-packer-templates

Templates for Packer.io to build CentOS Vagrant base boxes, taken from Chef/Bento project to preserve 'obsolete' versions for posterity.

Full credits for the templates, and base boxes go to https://github.com/chef/bento.

## Background

Vagrant is a very convenient way of creating and managing VMs. I was depending on chef/centos base boxes for my mlvagrant project. In August 2015 they were taken down, because the Chef organisation decided to stop publishing older versions, and moved from chef/ to bento/. The bento boxes include only latest release of each major version of the OS types they support (quite a lot of them). This was inconvenient for my mlvagrant project, and for anyone else that was depending on those base boxes. Next to this, I think it makes sense to preserve the older versions, to be able to run tests against old kernels as well.

Luckely all the good work for Bento is on github (https://github.com/chef/bento), and you can rebuild the VM images from the packer templates published in that project. I took the liberty to copy them (focussing on centos 64-bit), and also searched git history to pull up a number of the 'obsolete' versions of the CentOS templates.

## Building yourself

You will need to install Packer.io. If you are running MacOS with Homebrew you can run this:

- brew install caskroom/cask/brew-cask (See https://github.com/caskroom/homebrew-cask for details on Cask)
- brew cask update
- brew cask install packer

After that, grab the templates from this project, and run packer with a command like this:

- packer build -only=virtualbox-iso centos-6.7-x86_64.json

Or you could leverage the bento project (`git clone https://github.com/chef/bento.git`) itself:

- bin/bento build -o virtualbox-iso -m http://centos.mirror.transip.nl centos-*_64.json

This will take a while. It will help using a mirror that is near to you:

  http://isoredirect.centos.org/centos/6/isos/x86_64/

## Recovering from local vagrant cache

In the end I didn't need to rebuild them at all. Instead I was able to take them from my local Vagrant cache. That is actually pretty simple. It comes down to taking these few steps for each basebox:

- vagrant init chef/centos-6.5
- vagrant up --provider virtualbox
- vagrant package --output 1.0.0/virtualbox/packer-centos-6.5-x86_64-disk1.box
- vagrant destroy

Once you have .box files, you can upload these to atlas.hashicorp.com with following steps:

- Login or Sign up at https://atlas.hashicorp.com/account/new
- Click "Create a Vagrant Box with the Web UI"
- Pick a name for the box, check the private flag, and create new box
- Create new version
- Create new provider
- Select 'virtualbox' (or whatever is appropriate)
- And upload the .box file that you just created
- Once uploaded go back to the version page
- Edit version details, and click Release version there
- You can then test the box with vagrant using:
  - vagrant login
  - vagrant init {login/boxname}
  - vagrant up --provider virtualbox

## Available baseboxes

I republished the 4 Chef baseboxes I was using myself (only virtualbox unfortunately) on hashicorp.com for posterity, and have include newer bento boxes there as well:

https://atlas.hashicorp.com/grtjn:

- grtjn/centos-5.11 (bento 2.2.0)
- grtjn/centos-6.5 (chef 1.0.0)
- grtjn/centos-6.6 (chef 1.0.0)
- grtjn/centos-6.7 (bento 2.2.0)
- grtjn/centos-7.0 (chef 1.0.0)
- grtjn/centos-7.1 (chef 1.0.0/bento 2.2.0)
