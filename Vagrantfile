# -*- mode: ruby -*-
# vi: set ft=ruby :

# apart from the middleware node, create
# this many nodes in addition to the middleware
INSTANCES=2

# the nodes will be called middleware.example.net
# and node0.example.net, you can change this here
DOMAIN="example.net"

# these nodes do not need a lot of RAM, 384 is
# is enough but you can tweak that here
MEMORY=384

# the instances is a hostonly network, this will
# be the prefix to the subnet they use
SUBNET="192.168.2"

Vagrant::Config.run do |config|
  config.vm.define :middleware do |vmconfig|
    vmconfig.vm.box = "centos-64-x64-vbox4210-nocm"
    vmconfig.vm.network :hostonly, "#{SUBNET}.10"
    vmconfig.vm.host_name = "middleware.#{DOMAIN}"
    vmconfig.vm.customize ["modifyvm", :id, "--memory", MEMORY]
    vmconfig.vm.box_url = "http://vagrant.rocket.local/centos-64-x64-vbox4210-nocm.box"

    vmconfig.vm.provision :shell, :path => "epel_puppet.sh"
    vmconfig.vm.provision :puppet, :options => ["--pluginsync"], :module_path => "deploy/modules" do |puppet|
      puppet.manifests_path = "deploy"
      puppet.manifest_file = "site.pp"
    end
  end

  INSTANCES.times do |i|
    config.vm.define "node#{i}".to_sym do |vmconfig|
      vmconfig.vm.box = "centos-64-x64-vbox4210-nocm"
      vmconfig.vm.network :hostonly, "#{SUBNET}.%d" % (10 + i + 1)
      vmconfig.vm.customize ["modifyvm", :id, "--memory", MEMORY]
      vmconfig.vm.host_name = "node%d.#{DOMAIN}" % i
      vmconfig.vm.box_url = "http://vagrant.rocket.local/centos-64-x64-vbox4210-nocm.box"

      vmconfig.vm.provision :shell, :path => "epel_puppet.sh"
      vmconfig.vm.provision :puppet, :options => ["--pluginsync"], :module_path => "deploy/modules" do |puppet|
        puppet.manifests_path = "deploy"
        puppet.manifest_file = "site.pp"
      end
    end
  end
end
