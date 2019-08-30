# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT
mkdir -p /etc/puppet/modules

# FIX conflicting
# if [ ! -d /etc/puppet/modules/file_concat ]; then
# puppet module install ispavailability/file_concat
# fi

if [ ! -d /etc/puppet/modules/apt ]; then
puppet module install puppetlabs-apt
fi
if [ ! -d /etc/puppet/modules/java ]; then
puppet module install puppetlabs-java
fi
if [ ! -d /etc/puppet/modules/elasticsearch ]; then
puppet module install elasticsearch-elasticsearch
fi
if [ ! -d /etc/puppet/modules/logstash ]; then
puppet module install elasticsearch-logstash
fi
if [ ! -d /etc/puppet/modules/mongodb ]; then
puppet module install puppetlabs-mongodb
fi
if [ ! -d /etc/puppet/modules/gvm ]; then
puppet module install paulosuzart-gvm
fi
if [ ! -d /etc/puppet/modules/stdlib ]; then
# TODO remove version if not needed the live below
puppet module install puppetlabs-stdlib --version 4.21.0
fi
if [ ! -d /etc/puppet/modules/nodejs ]; then
puppet module install puppetlabs-nodejs
fi
if [ ! -d /etc/puppet/modules/wget ]; then
puppet module install maestrodev-wget
fi

SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # set to false, if you do NOT want to check the correct VirtualBox Guest Additions version when booting this box
  if defined?(VagrantVbguest::Middleware)
    config.vbguest.auto_update = true
  end

  config.vm.synced_folder ".", "/measurementor", create: "true"
  config.vm.box = "hashicorp/precise64"
  config.vm.box_version = "1.0.0"
  config.vm.hostname = "localhost"

  config.vm.network :forwarded_port, guest: 27017, host: 27017 #mongo
  config.vm.network :forwarded_port, guest: 28017, host: 28017 #mongo
  config.vm.network :forwarded_port, guest: 5601, host: 5601 #kibana
  config.vm.network :forwarded_port, guest: 9200, host: 9200 #elasticsearch
  config.vm.network :forwarded_port, guest: 9300, host: 9300 #elasticsearch
  config.vm.network :forwarded_port, guest: 8070, host: 8070 #so you can the grails app on your dev box

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--cpus", "2", "--memory", "4096"] #best practice for vagrant is to use 1/4 of the host's memory
    vb.gui = false  #if you want to see the screen, set this to true
  end

  # FIX attempt based on: https://blog.doismellburning.co.uk/upgrading-puppet-in-vagrant-boxes/
  config.vm.provision :shell, :path => "upgrade_puppet.sh"
  config.vm.provision "shell", inline: 'echo "START=yes" > /etc/default/puppet'
  config.vm.provision "shell", inline: $script
  config.vm.provision "puppet", manifests_path: "manifests", manifest_file: "default.pp" #, module_path: "/etc/puppet/modules"

  # installs VBox Guest Additions on the VM
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
    config.vbguest.no_install = true
    config.vbguest.no_remote = true
  end

  # TODO vagrant up still fails
  # https://serverfault.com/questions/882112/error-puppet-hash-expected-on-the-default-at-tmp-modules-apt-manifests-i
end
