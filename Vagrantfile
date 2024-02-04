# -*- mode: ruby -*-
# vim: set ft=ruby :
MACHINES = {
  :pam => {
        :box_name => "centos/8",
        :box_version => "2011.0",
        :ip_addr => "192.168.56.100",      
  },
}


Vagrant.configure("2") do |config|


  MACHINES.each do |boxname, boxconfig|

      config.vm.synced_folder ".", "/vagrant", disabled: false
      config.vm.define boxname do |box|
        box.vm.box = boxconfig[:box_name]
        box.vm.box_version = boxconfig[:box_version]
        box.vm.host_name = boxname.to_s
        box.vm.network "private_network", ip: boxconfig[:ip_addr]
        box.vm.provider :virtualbox do |vb|
              vb.customize ["modifyvm", :id, "--memory", "1024"]
        end
        box.vm.provision "shell", inline: <<-SHELL
          sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config
          systemctl restart sshd.service
          useradd testadm && useradd test
          echo "Test2024!" | passwd --stdin testadm && echo "Test2024!" | passwd --stdin test
          groupadd -f admin
          usermod testadm -a -G admin && usermod root -a -G admin && usermod vagrant -a -G admin
          cp /vagrant/login.sh /usr/local/bin/login.sh
          chmod +x /usr/local/bin/login.sh
          echo "session    optional     pam_exec.so seteuid stdout type=open_session /usr/local/bin/login.sh" >> /etc/pam.d/sshd
  	 SHELL
        #box.vm.provision "shell", path: "pam.sh"
    end
  end
end
