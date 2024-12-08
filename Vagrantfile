QTD_MAQUINAS=2
BOX_PADRAO="ubuntu/focal64"

Vagrant.configure("2") do |config|

  config.vm.box = BOX_PADRAO

  # config.vm.network "private_network", type: "dhcp"

  config.vm.define "bd" do |vmbd|
    vmbd.vm.network "private_network", ip: "172.28.128.8"
    vmbd.vm.network "forwarded_port", guest: 5432, host: 5432
    vmbd.vm.synced_folder "dados", "/persistencia", disabled: false

    vmbd.vm.provider "virtualbox" do |vb|
      vb.gui = false
    
      vb.name = "VM_Postgres"
      vb.memory = "1024"
      vb.cpus = 1
    end

    vmbd.vm.provision "docker" do |docker|
      docker.run "bitnami/postgresql:latest", args: "--name postgresql -p 5432:5432 -e POSTGRESQL_USERNAME=usr -e POSTGRESQL_PASSWORD=pass -e POSTGRESQL_DATABASE=db"
    end
  end

  (1..QTD_MAQUINAS).each do |n|
    config.vm.define "app#{n}" do |vmapp|

      vmapp.vm.box = BOX_PADRAO

      vmapp.vm.network "private_network", ip: "172.28.128.9#{n}"
      vmapp.vm.network "public_network", bridge: "Realtek PCIe GbE Family Controller"
      
      vmapp.vm.network "forwarded_port", guest: 80, host: "808#{n}"
      vmapp.vm.synced_folder "dados", "/persistencia", disabled: false

      vmapp.vm.provider "virtualbox" do |vb|
        vb.gui = false
      
        vb.name = "VM_Application#{n}"
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
end
