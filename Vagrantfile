ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'

Vagrant.configure("2") do |config|
    config.vm.define "lb" do |lb|
    lb.vm.network :private_network, ip: "192.168.10.10", netmask: 24
    lb.vm.network "forwarded_port", guest: 80, host: 80
    lb.vm.network "forwarded_port", guest: 443, host: 443
    lb.vm.provider "docker" do |lb|
    lb.build_dir = "./Load-balancer"
    lb.has_ssh = true
    lb.privileged = true
    lb.create_args = ["-v", "/sys/fs/cgroup:/sys/fs/cgroup:ro"]
            lb.name = "lb"
   end
  end
end

Vagrant.configure("2") do |config|
  config.vm.define "db" do |db|
  db.vm.network "private_network", type: "static", ip: "192.168.10.13"
  db.vm.provider "docker" do |db|
  db.build_dir = "./BDD"
  db.has_ssh = true
  db.privileged = true
  db.create_args = ["-v", "/sys/fs/cgroup:/sys/fs/cgroup:ro"]
          db.name = "db"
 end
end
end

  ws=2
  (1..ws).each do |i|
    Vagrant.configure("2") do |config|
    config.vm.define "w#{i}" do |w|
      w.vm.network "private_network", type: "static", ip: "192.168.10.1#{i}"
      w.vm.provider "docker" do |w|
      w.build_dir = "./WP"
      w.has_ssh = true
      w.privileged = true
      w.create_args = ["-v", "/sys/fs/cgroup:/sys/fs/cgroup:ro"]
        w.name = "w#{i}"
      end
    end
  end
end
