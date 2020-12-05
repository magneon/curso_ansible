$copy_private_key = <<-SCRIPT
  echo "Copying the key to guest"
  cp ./config/anspvtkey ~/.ssh/anspvtkey
  echo "Done"

  echo "Changing key owner"
  chown vagrant:vagrant ~/.ssh/anspvtkey
  echo "Done"

  echo "Changing privileges"
  chmod 600 ~/.ssh/anspvtkey
  echo "Done"
SCRIPT

Vagrant.configure("2") do |config|

  # Creating Ansible Controller machine
  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "ubuntu/focal64"
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
  end
end
