VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Configure The Box
  config.vm.box = 'centos/7'
  config.vm.hostname = 'qsggroup'

  # Don't Replace The Default Key https://github.com/mitchellh/vagrant/pull/4707
  config.ssh.insert_key = false
  config.ssh.forward_agent = true
  
  
  # Configure A Private Network IP
  config.vm.network :private_network, ip: "192.168.5.10"

  config.vm.provider :virtualbox do |vb|
    vb.customize ['modifyvm', :id, '--name', 'qsggroup']
    vb.customize ['modifyvm', :id, '--memory', '2048']
    vb.customize ["modifyvm", :id, "--cpus", "2"]
    vb.customize ['modifyvm', :id, '--natdnsproxy1', 'on']
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
  end

  # Configure Port Forwarding
  config.vm.network 'forwarded_port', guest: 80, host: 8000
  config.vm.network 'forwarded_port', guest: 3306, host: 33060
  config.vm.network 'forwarded_port', guest: 27017, host: 27017
  config.vm.network 'forwarded_port', guest: 6379, host: 6379

  #config.vm.synced_folder './', '/vagrant', mount_options: ["dmode=755", "fmode=666"]
  #owner: "nginx", group: "nginx",
  config.vm.synced_folder "./www", "/usr/share/nginx", mount_options: ["dmode=777", "fmode=777"]
  
  if File.file?(File.expand_path("~/.gitconfig"))
    config.vm.provision "file", source: "~/.gitconfig", destination: ".gitconfig"
  end
  
  # Configure The Public Key For SSH Access
  if File.exists? File.expand_path("~/.ssh/id_rsa.pub")
    config.vm.provision "shell" do |s|
      s.inline = "echo $1 | grep -xq \"$1\" /home/vagrant/.ssh/authorized_keys || echo $1 | tee -a /home/vagrant/.ssh/authorized_keys"
      s.args = [File.read(File.expand_path("~/.ssh/id_rsa.pub"))]
    end
  end
    
  # Copy The SSH Private Keys To The Box
  ["~/.ssh/id_rsa"].each do |key|
    config.vm.provision "shell" do |s|
      s.privileged = false
      s.inline = "echo \"$1\" > /home/vagrant/.ssh/$2 && chmod 600 /home/vagrant/.ssh/$2"
      s.args = [File.read(File.expand_path(key)), key.split('/').last]
    end
  end

  # Copy file from host to guest.
  #if File.file?(File.expand_path("./files/.bash_aliases"))
  #    config.vm.provision "file", source: "./files/.bash_aliases", destination: ".bash_aliases"
  #end


  # Run The Base Provisioning Script
  #config.vm.provision 'shell', path: './scripts/update.sh'
  #config.vm.provision :reload
  config.vm.provision 'shell', path: './scripts/provision.sh'
end
