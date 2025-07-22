# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  
  # Ubuntu 22.04 (Jammy) - VM por defecto
  config.vm.define "ubuntu", primary: true do |ubuntu|
    ubuntu.vm.box = "ubuntu/jammy64"
    ubuntu.vm.hostname = "vm-setup-ubuntu"
    
    ubuntu.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.cpus = 2
      vb.name = "vm-setup-ubuntu"
    end
    
    ubuntu.vm.provision :ansible do |ansible|
      ansible.galaxy_role_file = "ansible/requirements/roles.yml"
      ansible.playbook = "ansible/playbooks/vm-setup.yml"
      ansible.raw_ssh_args = ["-o ForwardAgent=yes"]
      ansible.extra_vars = {
        ansible_user: "vagrant",
        ansible_python_interpreter: "/usr/bin/python3",
        mw_tools_enabled: false
      }
    end
  end
  
  # Arch Linux
  config.vm.define "arch" do |arch|
    arch.vm.box = "generic/arch"
    arch.vm.hostname = "vm-setup-arch"
    
    arch.vm.provider "virtualbox" do |vb|
      vb.memory = "4096"
      vb.cpus = 2
      vb.name = "vm-setup-arch"
    end
    
    # Instalación mínima de Python (requerido por Ansible)
    arch.vm.provision "shell", inline: <<-SHELL
      # Solo instalar Python si no existe
      if [ ! -f /usr/bin/python3 ]; then
        pacman -Sy --noconfirm python
      fi
    SHELL
    
    arch.vm.provision :ansible do |ansible|
      ansible.galaxy_role_file = "ansible/requirements/roles.yml"
      ansible.playbook = "ansible/playbooks/vm-setup.yml"
      ansible.raw_ssh_args = ["-o ForwardAgent=yes"]
      ansible.extra_vars = {
        ansible_user: "vagrant",
        ansible_python_interpreter: "/usr/bin/python3",
        mw_tools_enabled: false
      }
    end
  end
end
