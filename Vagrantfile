Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 512
    vb.cpus = 1
  end

  config.vm.define "wordpress" do |wordpress|
    wordpress.vm.network "private_network", ip:"192.168.1.22"
    wordpress.vm.provision "shell", inline:"cat /vagrant/configs/idbionic.pub >> .ssh/authorized_keys"
    wordpress.vm.provider "virtualbox" do |wordpress_provider|
      wordpress_provider.name="wordpress_machine"
    end
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
      ansible wordpress --private-key /vagrant/idbionic -i /vagrant/configs/ansible/hosts -m shell -a 'echo Hello, World'
      "
    ansible.vm.provider "virtualbox" do |ansible_provider|
      ansible_provider.name="ansible_machine"
    end
  end

end