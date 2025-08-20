Vagrant.configure("2") do |config|

  # App VM
  #config.vm.define "app" do |app|
    app.vm.box = "ubuntu/bionic64"
    app.vm.hostname = "app"
    app.vm.network "forwarded_port", guest: 80, host: 80  # expose port 80 to host
    app.vm.network "private_network", ip: "192.168.56.10"
    app.vm.synced_folder "todo-app-main", "/vagrant/todo-app-main"
    app.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "ansible/playbook.yml"
      ansible.limit = "app"
    end
  end

  # DB VM
  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/bionic64"
    db.vm.hostname = "db"
    db.vm.network "private_network", ip: "192.168.56.11"
    db.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "ansible/playbook.yml"
      ansible.limit = "db"
    end
  end

end
