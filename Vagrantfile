$script_install_docker = <<-SCRIPT
  # echo Set up the repository

  # echo - 1 Update the apt package index and install packages to allow apt to use a repository over HTTPS:
  # apt-get update 

  # apt-get install \
  # apt-transport-https \
  # ca-certificates \
  # curl \
  # gnupg \
  # lsb-release -y

  # echo - 2 Add Docker’s official GPG key
  # curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

  # echo - 3 Use the following command to set up the stable repository.
  # echo \
  # "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  # $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

  # curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  # add-apt-repository "deb https://download.docker.com/linux/ubuntu bionic stable"
  # apt-get install software-properties-common -y

  # echo Install Docker Engine
  # echo - Update the apt package index, and install the latest version of Docker Engine and containerd
  # apt-get update
  # apt-get install docker-ce docker-ce-cli containerd.io -y
  apt-get update && apt-get install -y docker.io
SCRIPT

$script_change_permission_socket = <<-SCRIPT
  echo Change the permissions of docker socket
  chmod 666 /var/run/docker.sock
SCRIPT

$script_create_swarm_manager = <<-SCRIPT
  echo Swarm init
  docker swarm init --advertise-addr 192.168.1.100
  docker swarm join-token worker -q > /vagrant/worker_join_token
SCRIPT

$script_insert_worker_in_swarm = <<-SCRIPT
  echo Swarm join
  docker swarm join --token $(cat /vagrant/worker_join_token) 192.168.1.100:2377
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provision "shell", inline: $script_install_docker
  config.vm.provision "shell", inline: $script_change_permission_socket
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 512
    vb.cpus = 1
  end 

  config.vm.define "manager" do |manager|
    manager.vm.network "private_network", ip: "192.168.1.100"
    manager.vm.hostname = "swarm-manager"

    manager.vm.provider "virtualbox" do |vb|
        vb.name = "manager"
    end

    manager.vm.provision "shell", inline: $script_create_swarm_manager
  end

  (1..3).each do |i|
    config.vm.define "worker0#{i}" do |worker|
      worker.vm.network "private_network", ip: "192.168.1.10#{i}"
      worker.vm.hostname = "worker0#{i}"
      
      worker.vm.provider "virtualbox" do |vb|
        vb.name = "worker0#{i}"
      end

      worker.vm.provision "shell", inline: $script_insert_worker_in_swarm
    end
  end

end
