# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "db1" do |db|
    db.vm.box = "ubuntu/bionic64"
    db.vm.hostname = "mariadb1"
    db.vm.network "private_network", ip: "192.168.56.11"

    db.vm.provider "virtualbox" do |vb|
      vb.name = "mariadb1"
      vb.memory = "2048"
    end

    db.vm.provision "shell",
      inline: "apt-get update -y && apt-get install -y --no-install-recommends apt-utils make autoconf && apt-get clean -y"

    db.vm.provision "shell",
      privileged: false,
      inline: "ssh-keygen -t rsa -f ~/.ssh/id_rsa -P dicoding"

    db.vm.provision "shell",
      inline: "sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8"

    db.vm.provision "shell",
      inline: "sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] http://sgp1.mirrors.digitalocean.com/mariadb/repo/10.2/ubuntu bionic main' -y"

    db.vm.provision "shell",
      inline: "apt update && apt install mariadb-server -y && systemctl start mariadb.service"
  end
end
