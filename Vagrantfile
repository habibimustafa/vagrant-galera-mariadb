# -*- mode: ruby -*-
# vi: set ft=ruby :

GALERA_CLUSTER_IPS = ["192.168.56.11","192.168.56.12"]
GALERA_CLUSTER_PREFIX = "galera"
MYSQL_ROOT_PASSWORD = "12345678"

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
  end


  node_ips = GALERA_CLUSTER_IPS
  node_ips.each_with_index do |node_ip, index|
    box_hostname = "#{GALERA_CLUSTER_PREFIX}#{index+1}"
 
    config.vm.provider "virtualbox" do |vb|
      vb.name = "mariadb#{index+1}"
    end

    config.vm.define "db1" do |db|
      db.vm.hostname = box_hostname
      db.vm.network "private_network", ip: node_ip

      db.vm.provision "shell", run: "once", inline: <<-SHELL
        apt-get update -y
        apt-get install -y --no-install-recommends apt-utils make autoconf
        apt-get clean -y
      SHELL

      db.vm.provision "shell",
        run: "once",
        privileged: false,
        inline: "ssh-keygen -t rsa -f ~/.ssh/id_rsa -P dicoding"

      db.vm.provision "shell", run: "once", inline: <<-SHELL
        sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8
        sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] http://sgp1.mirrors.digitalocean.com/mariadb/repo/10.2/ubuntu bionic main'
        apt-get -q -y update
        export DEBIAN_FRONTEND=noninteractive
        debconf-set-selections <<< 'mariadb-server-10.2 mysql-server/root_password password #{MYSQL_ROOT_PASSWORD}'
        debconf-set-selections <<< 'mariadb-server-10.2 mysql-server/root_password_again password #{MYSQL_ROOT_PASSWORD}'
        apt-get install -y -q mariadb-server
        service mysql stop
        ufw allow 3306,4567,4568,4444/tcp
        ufw allow 4567/udp
        cp /vagrant/galera.cnf /etc/mysql/conf.d/galera.cnf      
      SHELL

			replace_hostname_cmd = "sed -i 's/THIS_HOSTNAME/#{box_hostname}/' /etc/mysql/conf.d/galera.cnf"
			replace_all_ips_cmd = "sed -i 's/ALL_IPS/#{node_ips.join(',')}/' /etc/mysql/conf.d/galera.cnf"
			replace_this_ip_cmd = "sed -i 's/THIS_IP/#{node_ip}/' /etc/mysql/conf.d/galera.cnf"

			db.vm.provision :shell, run: 'once', inline: replace_hostname_cmd
			db.vm.provision :shell, run: 'once', inline: replace_all_ips_cmd
			db.vm.provision :shell, run: 'once', inline: replace_this_ip_cmd

			if index == 0
				db.vm.provision :shell, run: 'once', inline: 'galera_new_cluster'
			else
				db.vm.provision :shell, run: 'once', inline: 'service mysql start'
			end
    end
  end
end
