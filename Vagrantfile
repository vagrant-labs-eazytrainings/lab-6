# Variables globales
CPU = 1
RAM = "1024"
OS = "ubuntu/jammy64"
NUM_MACHINES = 2

Vagrant.configure("2") do |config|
  # Configuration globale du provider
  config.vm.provider "virtualbox" do |vb|
    vb.memory = RAM
    vb.cpus = CPU
  end

  # Load Balancer
  config.vm.define "lb" do |lb|
    lb.vm.box = OS
    lb.vm.network "private_network", ip: "10.0.0.10"
    lb.vm.hostname = "lb.local"
    lb.hostsupdater.aliases = ["lb.example"]
  end

  # Serveurs Web (boucle dynamique)
  (1..NUM_MACHINES).each do |i|
    config.vm.define "web#{i}" do |web|
      web.vm.box = OS
      web.vm.network "private_network", ip: "10.0.0.#{10 + i}"
      web.vm.hostname = "web#{i}.local"
      web.hostsupdater.aliases = ["web#{i}.example"]
    end
  end
end
