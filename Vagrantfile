Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/focal64"

  config.vm.define "app" do |vmapp|

    vmapp.vm.box = "ubuntu/focal64"

    vmapp.vm.network "forwarded_port", guest: 80, host: 8080

    vmapp.vm.synced_folder "dados", "/persistencia", disabled: false

    vmapp.vm.provider "virtualbox" do |vb|
      vb.gui = false
    
      vb.name = "Application"
      vb.memory = "1024"
      vb.cpus = 2
      vb.customize ["modifyvm", :id, "--cpuexecutioncap", "100"]
    end

    vmapp.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y nginx
      rm /var/www/html/index.nginx-debian.html
      cp /persistencia/index.nginx-debian.html /var/www/html/
    SHELL

    vmapp.vm.provision "shell", path: "install_nodejs.sh"
  end

end
