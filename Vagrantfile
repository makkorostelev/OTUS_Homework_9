ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'
# -*- mode: ruby -*-
# vi: set ft=ruby :
hosts = {
  "consul-0" => "192.168.56.7",
  "consul-1" => "192.168.56.8",
  "consul-2" => "192.168.56.9",
  "nginx-0" => "192.168.56.10",
  "nginx-1" => "192.168.56.11",
  "backend-0" => "192.168.56.12",
  "backend-1" => "192.168.56.13",
  "database-0" => "192.168.56.14",
  "database-1" => "192.168.56.15",
  "database-2" => "192.168.56.16"
}
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.ssh.insert_key = false
 hosts.each do |name, ip|
    config.vm.define name do |machine|
      machine.vm.hostname = name
      machine.vm.network :private_network, ip: ip
      machine.vm.provider "virtualbox" do |v|
        v.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
        v.name = name
        v.gui = false
        v.memory = "2048"
        v.cpus = 2
      end
      if name == "database-2"
        machine.vm.provision :ansible do |ansible|
          ansible.compatibility_mode = "2.0"
          ansible.limit = "all"
          ansible.playbook = "web-service.yml"
          ansible.groups = {
            "consul-servers" => ["consul-[0:2]"],
            "backend" => ["backend-[0:1]"],
            "database" => ["database-[0:2]"],
            "nginx" => ["nginx-[0:1]"],
            "consul-clients:children" => ["backend", "database", "nginx"]
          }
        end
      end
    end
  end
end