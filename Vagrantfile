# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/focal64"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "public_network", ip: "192.168.1.17"
  config.vm.synced_folder "./html", "/var/www/html"
  config.vm.provision "shell", inline: <<-SHELL
    apt update
    apt install -y nginx
    apt install net-tools
  SHELL
end
