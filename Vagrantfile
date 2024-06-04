# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  config.vm.network "private_network", ip: "192.168.56.10"

  config.vm.synced_folder "./html", "/var/www/html"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Leer la clave pública desde el archivo en el directorio del proyecto
  public_key = File.read(File.expand_path("ssh/new_vagrant_key.pub"))

  # Agregar una provisión de shell para crear un nuevo usuario y configurar SSH
  config.vm.provision "shell", inline: <<-SHELL
    # Crear un nuevo usuario
    sudo useradd -m -s /bin/bash newuser

    # Crear directorio .ssh para el nuevo usuario
    sudo mkdir -p /home/newuser/.ssh

    # Agregar la clave pública al archivo authorized_keys del nuevo usuario
    echo "#{public_key}" | sudo tee -a /home/newuser/.ssh/authorized_keys

    # Ajustar permisos
    sudo chown -R newuser:newuser /home/newuser/.ssh
    sudo chmod 700 /home/newuser/.ssh
    sudo chmod 600 /home/newuser/.ssh/authorized_keys

    # Configurar sudo sin contraseña para el nuevo usuario (opcional)
    echo "newuser ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/newuser
  SHELL

  # Configurar Ansible para usar el playbook
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
  end
end
