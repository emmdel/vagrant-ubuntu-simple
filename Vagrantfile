# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "geerlingguy/ubuntu1204"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 80, host: 8080, auto_correct: true
  # Mysql
  config.vm.network :forwarded_port, guest: 3306, host: 3308, auto_correct: true
  # Mongodb
  config.vm.network :forwarded_port, guest: 27017, host: 27017, auto_correct: true

  # Xdebug
  #config.vm.network :forwarded_port, guest: 9000, host: 9000, auto_correct: true

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # Static IP is required to use NFS shared folder
  config.vm.network :private_network, ip: "192.168.2.80"


  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # http://stackoverflow.com/a/16127657/1584930
  config.ssh.private_key_path = ["~/.ssh/id_rsa", "~/.vagrant.d/insecure_private_key"]
  config.ssh.forward_agent = true

  config.vm.synced_folder "./", "/vagrant", nfs: true, :mount_options => ["rsize=8192", "wsize=8192"]

  config.vm.provider :virtualbox do |vb|
    # Don't boot with headless mode
    #vb.gui = true

    host = RbConfig::CONFIG['host_os']

    # Give VM 1/4 system memory & access to all cpu cores on the host
    if host =~ /darwin/
      cpus = `sysctl -n hw.ncpu`.to_i
      # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
    elsif host =~ /linux/
      cpus = `nproc`.to_i
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
    else # sorry Windows folks, I can't help you
      cpus = 2
      mem = 1024
    end

    vb.customize ["modifyvm", :id, "--memory", mem]
    #vb.customize ["modifyvm", :id, "--memory", "1024"]
    vb.customize ["modifyvm", :id, "--cpus", cpus]

    # Fixes slow dns resolution, see https://github.com/mitchellh/vagrant/issues/1807#issuecomment-19132198
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    vb.customize ["modifyvm", :id, "--ioapic", "on"]
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    # Configure cached packages to be shared between instances of the same base box.
    # More info on http://fgrehm.viewdocs.io/vagrant-cachier/usage
    config.cache.scope = :box
  end

  # config.vm.provision "ansible" do |ansible|
  #   ansible.playbook       = "provision/vagrant.yml"
  #   ansible.verbose        = "vv"
  #   ansible.sudo           = true
  #   ansible.extra_vars = {
  #     # Enable SSH forwarding for Git SSH checkouts
  #     ansible_ssh_args: '-o ForwardAgent=yes'
  #   }
  # end
end
