$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 512
    vb.cpus = 1
  end

  # config.vm.define "mysqldb" do |mysql|
    
  #   mysql.vm.network "public_network", ip:"192.168.1.24"
  #   mysql.vm.provision "shell", inline:"cat /configs/idbionic.pub >> .ssh/authorized_keys"
  #   mysql.vm.provision "shell", inline:$script_mysql
  #   mysql.vm.provision "shell", inline:"cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"
  #   mysql.vm.provision "shell", inline:"service mysql restart"
  #   mysql.vm.synced_folder "./configs", "/configs"
  #   mysql.vm.synced_folder "." "/vagrant", disabled: true
  # end

  config.vm.define "phpweb" do |phpweb|
    phpweb.vm.network "forwarded_port", guest: 8888, host: 8888
    phpweb.vm.network "private_network", ip:"192.168.1.25"

    phpweb.vm.provider "virtualbox" do |vb|
      vb.name = "ubuntu_bionic_php"
      vb.memory = 1024
      vb.cpus = 1
    end

    phpweb.vm.provision "shell", inline:"apt-get update && apt-get install -y puppet"
    phpweb.vm.provision "puppet" do |puppet|
      puppet.manifests_path = "./configs/manifests"
      puppet.manifest_file = "phpweb.pp"
    end
  end

  config.vm.define "mysqlserver" do |mysqlserver|
    mysqlserver.vm.network "private_network", ip:"192.168.1.22"
    mysqlserver.vm.provision "shell", inline:"cat /vagrant/configs/idbionic.pub >> .ssh/authorized_keys"
  end

  config.vm.define "ansible" do |ansible|
    ansible.vm.network "private_network", ip:"192.168.1.26"

    ansible.vm.provision "shell", inline:"cp /vagrant/idbionic /home/vagrant && \
      chmod 600 /home/vagrant/idbionic && \
      chown vagrant:vagrant /home/vagrant/idbionic
      "

    ansible.vm.provision "shell", inline:"apt-get update && \
      apt install -y software-properties-common && \
      add-apt-repository --yes --update ppa:ansible/ansible && \
      apt install -y ansible && \
      ansible-playbook -i /vagrant/configs/ansible/hosts /vagrant/configs/ansible/playbook.yml
      "
  end

  config.vm.define "memcached" do |memcached|
    memcached.vm.box = "centos/7"
    memcached.vm.provider "virtualbox" do |vb|
      vb.memory = 512
      vb.cpus = 1
      vb.name = "centos7_memcached"
    end
  end

end