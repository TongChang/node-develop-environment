# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 80, host: 8888

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder "./sync", "/tmp/vagrant"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provider "virtualbox" do |vb|
    vb.name = "Ubuntu and node.js and MySQL"
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end

  config.vm.provision "shell", inline: <<-SHELL

    # TODO set timezone

    # apt-get
    apt-get upgrade
    apt-get update
    apt-get install -y git perl vim jq tig sysv-rc-conf zsh

    # setting zsh
    chsh -s /bin/zsh
    chsh -s /bin/zsh vagrant

    # setting prezto
    git clone --recursive https://github.com/sorin-ionescu/prezto.git /home/vagrant/.zprezto
    setopt EXTENDED_GLOB
    for rcfile in `\find /home/vagrant/.zprezto/runcoms/ -maxdepth 1 -type f | grep -v README.md `; do
      ln -s $rcfile /home/vagrant/.`basename $rcfile`
    done

    # setup ssh key for github
    mkdir /home/vagrant/.ssh
    cp -p /tmp/vagrant/ssh-key/id_rsa /home/vagrant/.ssh/
    chmod 600 /home/vagrant/.ssh/id_rsa
    chown vagrant:vagrant /home/vagrant/.ssh/id_rsa


    # setup user for git
    su -c 'git config --global user.email "tong1212chang@gmail.com"' vagrant
    su -c 'git config --global user.name "TongChang"' vagrant

    # setup git editor
    su -c 'git config --global core.editor vim' vagrant

    # setup dotfiles
    echo -e "Host github.com\n\tStrictHostKeyChecking no\n" >> /home/vagrant/.ssh/config
    su -c 'git clone https://github.com/TongChang/dotfiles.git /home/vagrant/dotfiles/' vagrant
    ln -s /home/vagrant/dotfiles/vim/_vimrc /home/vagrant/.vimrc
    ln -s /home/vagrant/dotfiles/zsh/_zshrc /home/vagrant/.zshrc

    # install nodebrew
    su -c 'wget http://git.io/nodebrew -P /home/vagrant/.nodebrew/' vagrant
    su -c 'chmod 755 /home/vagrant/.nodebrew/nodebrew' vagrant
    su -c '/home/vagrant/.nodebrew/nodebrew setup' vagrant
    su -c '/home/vagrant/.nodebrew/nodebrew install-binary stable' vagrant
    su -c '/home/vagrant/.nodebrew/nodebrew use stable' vagrant
    su -c 'echo "export PATH=/home/vagrant/.nodebrew/current/bin:\\$PATH" >> /home/vagrant/.zshrc' vagrant

    # makedir
    mkdir /home/vagrant/work

    # install mysql
    echo "mysql-server-5.6 mysql-server/root_password password root" | sudo debconf-set-selections
    echo "mysql-server-5.6 mysql-server/root_password_again password root" | sudo debconf-set-selections

    apt-get -y install mysql-server
    cp /etc/mysql/my.cnf /etc/mysql/my.cnf.org
    cp /tmp/vagrant/mysql/my.cnf /etc/mysql/my.cnf

    service mysql start
    sysv-rc-conf mysql on

    # reboot
    reboot

  SHELL

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
