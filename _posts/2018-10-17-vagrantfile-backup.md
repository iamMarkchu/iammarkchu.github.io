---
layout: post
title:  "VagrantFile备份"
categories: [环境配置]
tags: [vagrant, 配置文件]
---
## 配置
```ruby
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "homestead"
  config.vm.hostname = "mark-dev"
  config.vm.network "forwarded_port", guest: 80, host: 8888 ,id: 'nginx'
  config.vm.network "forwarded_port", guest: 8888, host: 8889 ,id: 'apache'
  config.vm.network "forwarded_port", guest: 8080, host: 8080 ,id: 'webpack-dev-js'
  config.vm.network "private_network", ip: "192.168.33.101",auto_config: true
  config.vm.synced_folder "/Users/mark/code", "/app", :nfs => true

  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
      vb.memory = "1024"
      vb.cpus = 2
      vb.name = "mark-dev"
  end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```