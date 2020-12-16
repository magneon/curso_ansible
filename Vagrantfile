$copy_private_key = <<-SCRIPT
  echo "Copying the key to guest"
  cp /configs/anskey /home/vagrant/.ssh/anskey
  echo "Done"

  echo "Changing key owner"
  chown vagrant:vagrant /home/vagrant/.ssh/anskey
  echo "Done"

  echo "Changing privileges"
  chmod 600 /home/vagrant/.ssh/anskey
  echo "Done"
SCRIPT

$install_ansible = <<-SCRIPT
  echo "Installing Ansible at Host"
  apt-get update >> ansible_install.log
  apt-get install -y software-properties-common >> ansible_install.log
  apt-add-repository --yes --update ppa:ansible/ansible >> ansible_install.log
  apt-get install -y ansible >> ansible_install.log
  echo "Done"
SCRIPT

$copy_public_key = <<-SCRIPT
  echo "Copying the public key"
  cat /configs/anskey.pub >> .ssh/authorized_keys
  echo "Done"
SCRIPT

$install_python = <<-SCRIPT
  echo "Installing Python"
  apt-get update >> python_install.log && apt-get install python -y >> python_install.log
  echo "Done"
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/bionic64"

  # Creating Ansible Controller machine
  config.vm.define "ansible" do |ansible|
    ansible.vm.network "public_network", ip: "192.168.0.230"

    # Defining specific hardware configuration
    ansible.vm.provider "virtualbox" do |vb|
      vb.name = "ansible"
      vb.cpus = 1
      vb.memory = 512
    end

    # Disabling the default shared folder and creating a new one
    ansible.vm.synced_folder ".", "/vagrant", disabled: true
    ansible.vm.synced_folder "./configs", "/configs"

    # Providing the private key to the Ansible Controller
    ansible.vm.provision "shell", inline: $copy_private_key
    ansible.vm.provision "shell", inline: $install_ansible
  end

  # Creating Wordpress machine
  config.vm.define "wordpress" do |wordpress|
    wordpress.vm.network "public_network", ip: "192.168.0.240"

    # Defining specific hardware configuration
    wordpress.vm.provider "virtualbox" do |vb|
      vb.name = "wordpress"
      vb.cpus = 4
      vb.memory = 2048
    end

    wordpress.vm.synced_folder "./configs", "/configs"
    wordpress.vm.provision "shell", inline: $copy_public_key
    wordpress.vm.provision "shell", inline: $install_python
  end

  config.vm.define "mysql" do |mysql|
    mysql.vm.provider "virtualbox" do |vb|
      vb.name = "mysql"
      vb.cpus = 4
      vb.memory = 2048
    end

    mysql.vm.network "public_network", ip: "192.168.0.250"
    mysql.vm.synced_folder "./configs", "/configs"
    mysql.vm.provision "shell", inline: $copy_public_key
    mysql.vm.provision "shell", inline: $install_python
  end
end
