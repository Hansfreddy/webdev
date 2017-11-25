Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.network "private_network", ip: "192.168.56.101"
  config.vm.network "public_network"
  config.vm.hostname = "webdev"  
  
  config.vm.provider "virtualbox" do |v|
    v.gui = true
    v.memory = "2048"
  end
  
  config.vm.provision "shell", inline: <<-SHELL
  
    KEYBOARDLAYOUT="ch"
  
    apt-get update
    apt-get install -y --no-install-recommends ubuntu-desktop
    apt-get install -y virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11
    L=$KEYBOARDLAYOUT && sed -i 's/XKBLAYOUT=\"\w*"/XKBLAYOUT=\"'$L'\"/g' /etc/default/keyboard    
    reboot    

  SHELL

end
