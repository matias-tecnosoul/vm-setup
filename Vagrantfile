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
    
    # Preparación específica para Arch Linux
    arch.vm.provision "shell", inline: <<-SHELL
      # Instalar Python si no existe (requerido por Ansible)
      if [ ! -f /usr/bin/python3 ]; then
        pacman -Sy --noconfirm python
      fi
      
      # Pre-instalar vagrant desde AUR para evitar fallo en el role
      if ! command -v vagrant &> /dev/null; then
        echo "Installing vagrant from AUR..."
        
        # Instalar dependencias para compilar
        pacman -S --needed --noconfirm base-devel git
        
        # Crear usuario temporal para makepkg (no puede correr como root)
        useradd -m builduser
        echo 'builduser ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers
        
        # Compilar e instalar vagrant
        sudo -u builduser bash -c '
          cd /tmp
          git clone https://aur.archlinux.org/vagrant.git
          cd vagrant
          makepkg -si --noconfirm
        '
        
        echo "Vagrant installed successfully from AUR"
      fi
    SHELL
    
    arch.vm.provision :ansible do |ansible|
      ansible.galaxy_role_file = "ansible/requirements/roles.yml"
      ansible.playbook = "ansible/playbooks/vm-setup.yml"
      ansible.raw_ssh_args = ["-o ForwardAgent=yes"]
      ansible.extra_vars = {
        ansible_user: "vagrant",
        ansible_python_interpreter: "/usr/bin/python3",
        mw_tools_enabled: false,
        # Override packages list without problematic ones
        workstation_packages: [
          "base-devel", "bzip2", "python", "openssh", "wget", "curl", 
          "nmap", "jq", "unzip", "yamllint", "git", "git-lfs", "vim", 
          "tmux", "openfortivpn", "openconnect", "shellcheck", "bind", 
          "ppp", "haveged", "asciinema", "tree", "swaks", "podman", 
          "zsh", "xsel", "xclip", "openssl"
        ]
      }
    end
  end
end