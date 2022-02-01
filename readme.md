ssh-keygen -t rsa
openssl genrsa -out id_rsa 1024
ssh-keygen.exe -y -f id_rsa > id_rsa.pub

vagrant up

ansible-playbook /vagrant/provisioning.yml -i /vagrant/configs/ansible/hosts

