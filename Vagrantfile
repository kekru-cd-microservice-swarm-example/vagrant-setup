Vagrant.configure(2) do |config|
  
  config.vm.box = "gbarbieru/xenial"
  
  #Boot Timeout ist 10 Minuten (600 seconds)
  config.vm.boot_timeout = 600
  
  #config.ssh.password = "vagrant"
  
  #Initiierungsscript fuer alle VMs, Docker instalieren, etc.
  config.vm.provision "shell", inline: "sudo chmod -R 555 /vagrant/vm-init-scripts"
  config.vm.provision "shell", inline: "/vagrant/vm-init-scripts/init-for-all"
  
  #Die VMs erhalten jeweils 2GB RAM
  config.vm.provider :virtualbox do |p|
    p.customize ["modifyvm", :id, "--memory", 2048]
  end
  
  #-------------------------------------------------------------------------------------------
  #-------------------------------------------------------------------------------------------  
  
  #Testumgebung generieren
  config.vm.define "manager1" do |machine|
    #Der Manager bekommt 3GB RAM
	machine.vm.provider :virtualbox do |p|
		p.customize ["modifyvm", :id, "--memory", 3072]
	end
  
	#IP und DNS Name setzen
    machine.vm.hostname = "manager1"
	machine.vm.network "private_network", ip: "10.1.6.210"
	machine.vm.provision :hosts, :sync_hosts => true #DNS für die drei Maschinen. Hierfuer muss vagrant-hosts installiert werden: "vagrant plugin install vagrant-hosts"
	
	#Swarm fuer Testumgebung erstellen, Jenkins aufsetzen usw.
	machine.vm.provision "shell", inline: "/vagrant/vm-init-scripts/init-manager1"
  end

  config.vm.define "worker1" do |machine|
    #IP und DNS Name setzen
    machine.vm.hostname = "worker1"
	machine.vm.network "private_network", ip: "10.1.6.211"
	machine.vm.provision :hosts, :sync_hosts => true
	
	#Testumgebungs-Swarm als Worker beitreten. Der Swarm Manager hat den Join Token in einer Datei hinterlegt
	machine.vm.provision "shell", inline: "sh -c 'docker swarm join --token $(cat /vagrant/vm-data/swarmtokens/test-worker-token) 10.1.6.210:2377; true'"
  end
  
  config.vm.define "worker2" do |machine|
    #IP und DNS Name setzen
    machine.vm.hostname = "worker2"
	machine.vm.network "private_network", ip: "10.1.6.212"
	machine.vm.provision :hosts, :sync_hosts => true
	
	#Testumgebungs-Swarm als Worker beitreten. Der Swarm Manager hat den Join Token in einer Datei hinterlegt
	machine.vm.provision "shell", inline: "sh -c 'docker swarm join --token $(cat /vagrant/vm-data/swarmtokens/test-worker-token) 10.1.6.210:2377; true'"
  end
  
  
  #-------------------------------------------------------------------------------------------
  #-------------------------------------------------------------------------------------------
  
  #Erstelle Produktivumgebung
  config.vm.define "prodmanager1" do |machine|
	#IP und DNS Name setzen
    machine.vm.hostname = "prodmanager1"
	machine.vm.network "private_network", ip: "10.1.6.213"
	machine.vm.provision :hosts, :sync_hosts => true

    #Produktiv-Swarm initiieren
	machine.vm.provision "shell", inline: "/vagrant/vm-init-scripts/init-prodmanager1"
  end
  
  config.vm.define "prodworker1" do |machine|
    #Der Prod-Worker erhaelt 1GB RAM
	machine.vm.provider :virtualbox do |p|
		p.customize ["modifyvm", :id, "--memory", 1024]
	end
	
	#IP und DNS Name setzen
    machine.vm.hostname = "prodworker1"
	machine.vm.network "private_network", ip: "10.1.6.214"
	machine.vm.provision :hosts, :sync_hosts => true
	
	#Dem Produktiv Swarm als Worker beitreten. Der Swarm Manager hat den Join Token in einer Datei hinterlegt
	machine.vm.provision "shell", inline: "sh -c 'docker swarm join --token $(cat /vagrant/vm-data/swarmtokens/prod-worker-token) 10.1.6.213:2377; true'"
  end

end
