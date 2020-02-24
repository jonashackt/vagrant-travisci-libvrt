# vagrant-travisci

[![Build Status](https://travis-ci.org/jonashackt/vagrant-travisci.svg?branch=master)](https://travis-ci.org/jonashackt/vagrant-travisci)

Example project showing how to run Vagrant on TravisCI 


## Why Vagrant on a CI system?

I´d really want to test bigger Infrastructure-as-Code projects like https://github.com/jonashackt/kubernetes-the-ansible-way and therefore need Vagrant running on a CI system (I don´t want to setup or host the CI system myself).

And no, Docker-in-Docker won´t suffice here!

Well until today, I really thought that __there is no way to do it with TravisCI__ - just have a look into https://github.com/jonashackt/vagrant-ansible-on-appveyor ([and this so thread](https://stackoverflow.com/questions/31828555/using-vagrant-on-cloud-ci-services)).

But then I came upon these GitHub issues in my beloved Molecule project: https://github.com/ansible-community/molecule-vagrant/issues/2#issuecomment-585616279 & especially https://github.com/ansible-community/molecule-vagrant/issues/8#issuecomment-589902704, which confused me right away.

Did you know [libvirt](https://libvirt.org/)??! I didn't, this thing is a crazy thing - [an API for all those virtualization providers out there](https://help.ubuntu.com/lts/serverguide/libvirt.html) (sounds like Vagrant, huh?!). And as the GitHub Issue comments state, it should be possible to use Vagrant with libvrt & KVM on TravisCI... Which would give us the following workflow for our tools:

![cloud-uml](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/jonashackt/vagrant-travisci/master/cloud.iuml)
![local-uml](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/jonashackt/vagrant-travisci/master/local.iuml)

- __ONLY IF__ there are Vagrant Boxes that support both `libvrt` & `virtualbox` as a provider. And... there are! Just have a look at the `generic` boxes on the Vagrant Cloud: https://app.vagrantup.com/boxes/search?provider=libvirt&q=ubuntu+bionic&sort=downloads&utf8=%E2%9C%93, which are backed by https://roboxes.org

This would fulfil our request: in both cases a simple `vagrant up` based on the same [Vagrantfile](Vagrantfile) would work.

## Using vagrant-libvirt to run Vagrant with libvrt & KVM on TravisCI

First we need to configure the usual Travis [.travis.yml](.travis.yml) for our project:

```yaml
dist: bionic
language: python

install:
# Install libvrt & KVM (see https://github.com/alvistack/ansible-role-virtualbox/blob/master/.travis.yml)
- sudo apt-get update && sudo apt-get install -y bridge-utils dnsmasq-base ebtables libvirt-bin libvirt-dev qemu-kvm qemu-utils ruby-dev

# Download Vagrant & Install Vagrant package
- sudo wget -nv https://releases.hashicorp.com/vagrant/2.2.7/vagrant_2.2.7_x86_64.deb
- sudo dpkg -i vagrant_2.2.7_x86_64.deb

# Vagrant correctly installed?
- vagrant --version

# Install vagrant-libvirt Vagrant plugin
- sudo vagrant plugin install vagrant-libvirt
```
 
Then we also need to install [vagrant-libvirt](https://github.com/vagrant-libvirt/vagrant-libvirt) on TravisCI.


## Finally testdrive the Vagrant installation

Add the following `script` section to our [.travis.yml](.travis.yml):

```yaml
script:
- vagrant up
- vagrant ssh -c "echo 'hello world!'"
```