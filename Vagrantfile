# -*- mode: ruby -*-
# vi: set ft=ruby :

GALERA_CLUSTER_IPS = ["192.168.56.101","192.168.56.102","192.168.56.103"]
GALERA_CLUSTER_PREFIX = "galera"
MYSQL_ROOT_PASSWORD = "12345678"

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider "virtualbox" do |vb|
    vb.name = "mariadb1"
    vb.memory = "2048"
  end


  node_ips = GALERA_CLUSTER_IPS
  node_ips.each_with_index do |node_ip, index|
    box_hostname = "#{GALERA_CLUSTER_PREFIX}#{index+1}"
 
    config.vm.define "db1" do |db|
      db.vm.hostname = box_hostname
      db.vm.network "private_network", ip: node_ip

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
end
