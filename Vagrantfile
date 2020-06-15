Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.define "webserver" do |webserver|
    webserver.vm.network "private_network", ip: "192.168.10.100"
    webserver.vm.hostname = "webserver"
    webserver.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
    end
  end

  config.vm.define "server" do |server|
    server.vm.network "private_network", ip: "192.168.10.110"
    server.vm.hostname = "server"
    server.vm.provision "ansible" do |ansible|
      ansible.playbook = "loops.yml"
    end
  end
end