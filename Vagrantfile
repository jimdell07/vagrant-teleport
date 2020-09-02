Vagrant.configure("2") do |config|
  config.vm.synced_folder ".", "/home/vagrant/for_teleport"
  config.vm.provision :shell, :inline => <<-SHELL
    sudo apt update
    wget https://get.gravitational.com/teleport_4.3.5_amd64.deb &>1
    sudo dpkg -i teleport_4.3.5_amd64.deb
    sed -i '$ d' /etc/hosts
    sed -i '$ d' /etc/hosts
    sudo echo '192.168.33.35 authserver.test.com authserver' >> /etc/hosts
    sudo echo '192.168.33.36 proxyserver.test.com proxyserver' >> /etc/hosts
    sudo echo '192.168.33.36 my-site.example' >> /etc/hosts
    sudo mkdir -p /var/lib/teleport
    sudo cp /home/vagrant/for_teleport/teleport.service /etc/systemd/system/teleport.service
    sudo systemctl daemon-reload
    if [[ $(hostname) == authserver ]]; then cp /home/vagrant/for_teleport/auth.yaml /etc/teleport.yaml; else cp /home/vagrant/for_teleport/proxy.yaml /etc/teleport.yaml; fi
    sudo systemctl enable teleport
  SHELL

  config.vm.define "auth" do |auth|
    auth.vm.box = "ubuntu/bionic64"
    auth.vm.hostname = 'authserver'

    auth.vm.network :private_network, ip: "192.168.33.35"

    auth.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ["modifyvm", :id, "--name", "auth"]
    end
  end

  config.vm.define "proxy" do |proxy|
    proxy.vm.box = "ubuntu/bionic64"
    proxy.vm.hostname = 'proxyserver'

    proxy.vm.network :private_network, ip: "192.168.33.36"

    proxy.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--memory", 2048]
      v.customize ["modifyvm", :id, "--name", "proxy"]
    end
  end
end
