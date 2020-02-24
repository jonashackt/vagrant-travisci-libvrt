# vagrant-travisci

Example project showing how to run Vagrant on TravisCI 


## Why Vagrant on a CI system?

I´d really want to test bigger Infrastructure-as-Code projects like https://github.com/jonashackt/kubernetes-the-ansible-way and therefore need Vagrant running on a CI system (I don´t want to setup or host the CI system myself).

And no, Docker-in-Docker won´t suffice here!

Well until today, I really thought that __there is no way to do it with TravisCI__ - just have a look into https://github.com/jonashackt/vagrant-ansible-on-appveyor ([and this so thread](https://stackoverflow.com/questions/31828555/using-vagrant-on-cloud-ci-services)).

But then I came upon these GitHub issues in my beloved Molecule project: https://github.com/ansible-community/molecule-vagrant/issues/2#issuecomment-585616279 & especially https://github.com/ansible-community/molecule-vagrant/issues/8#issuecomment-589902704, which confused me right away.

Did you know [libvirt](https://libvirt.org/)??! I didn't, this thing is a crazy thing - [an API for all those virtualization providers out there](https://help.ubuntu.com/lts/serverguide/libvirt.html) (sounds like Vagrant, huh?!). And as the GitHub Issue comments state, it should be possible to use Vagrant with libvrt & KVM on TravisCI... Which would give us the following workflow for our tools:

![cloud-uml](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/jonashackt/vagrant-travisci/master/cloud.iuml)
![local-uml](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.githubusercontent.com/jonashackt/vagrant-travisci/master/local.iuml)