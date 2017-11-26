Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.56.101"
  config.vm.network "public_network"
  config.vm.hostname = "webdev"
  
  config.vm.provision "file", source: "vmfiles/MySQL workbench.desktop", destination: "/tmp/MySQL workbench.desktop"
  config.vm.provision "file", source: "vmfiles/Terminal.desktop", destination: "/tmp/Terminal.desktop"
  config.vm.provision "file", source: "vmfiles/tomcat.service", destination: "/tmp/tomcat.service"
  config.vm.provision "file", source: "vmfiles/catalina.opts", destination: "/tmp/catalina.opts"
  
  config.vm.provider "virtualbox" do |v|
    v.gui = true
    v.memory = "2048"
    v.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
    v.customize ["modifyvm", :id, "--draganddrop", "bidirectional"]
  end
  
  config.vm.provision "shell", inline: <<-SHELL    
  
    mkdir -p /home/ubuntu/Desktop
    mkdir -p /opt/tomcat/bin/
    
    cp "/tmp/MySQL workbench.desktop" "/home/ubuntu/Desktop/MySQL workbench.desktop"
    cp "/tmp/Terminal.desktop" "/home/ubuntu/Desktop/Terminal.desktop"
    cp /tmp/tomcat.service /etc/systemd/system/tomcat.service
    cp /tmp/catalina.opts /opt/tomcat/bin/setenv.sh
    
    chmod +x "/home/ubuntu/Desktop/MySQL workbench.desktop"
    chmod +x /home/ubuntu/Desktop/Terminal.desktop
    
    chown ubuntu:ubuntu "/home/ubuntu/Desktop/MySQL workbench.desktop"
    chown ubuntu:ubuntu /home/ubuntu/Desktop/Terminal.desktop
    chown tomcat:tomcat /opt/tomcat/bin/setenv.sh
 
    echo ubuntu:ubuntu | /usr/sbin/chpasswd
  
    echo "net.ipv6.conf.all.disable_ipv6 = 1
          net.ipv6.conf.default.disable_ipv6 = 1
          net.ipv6.conf.lo.disable_ipv6 = 1
          net.ipv6.conf.eth0.disable_ipv6 = 1" >> /etc/sysctl.conf
    sysctl -p
  
    echo 'Acquire::ForceIPv4 "true";' | sudo tee --append /etc/apt/apt.conf.d/99force-ipv4sudo
    
    add-apt-repository -y ppa:webupd8team/java
    apt-get update && apt-get upgrade && apt-get clean
    echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections
    apt-get install -y oracle-java8-installer
    
    apt-get install -y --no-install-recommends ubuntu-desktop
    apt-get install -y gnome-terminal
    apt-get install -y virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11
    apt-get install -y samba-common samba      
    
    groupadd tomcat
    useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    cd /tmp
    wget http://mirror.switch.ch/mirror/apache/dist/tomcat/tomcat-9/v9.0.1/bin/apache-tomcat-9.0.1.tar.gz 
    mkdir /opt/tomcat
    tar xvzf apache-tomcat-9.0.1.tar.gz -C /opt/tomcat --strip-components=1
    chown -R tomcat:tomcat /opt/tomcat
    
    echo "setxkbmap ch" >> /home/ubuntu/.bashrc    
    grep setxkbmap /home/ubuntu/.bashrc || echo "setxkbmap ch" >> /home/ubuntu/.bashrc
    
    debconf-set-selections <<< 'mysql-server mysql-server/root_password password root'
    debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password root'
    apt-get install -y mysql-server mysql-client mysql-workbench        
    
    cd /opt/tomcat
    chgrp -R tomcat /opt/tomcat
    chmod -R g+r conf
    chmod g+x conf
    chown -R tomcat webapps/ work/ temp/ logs/
    chmod +x /opt/tomcat/bin/setenv.sh
    systemctl daemon-reload
    systemctl start tomcat
    systemctl enable tomcat

    (echo tomcat; echo tomcat) | smbpasswd -a -s tomcat
    net usershare add tom /opt/tomcat "tomcat share" tomcat:f guest_ok=n
    
    reboot

  SHELL

end
