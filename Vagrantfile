Vagrant.configure("2") do |config|
  config.vm.define "app" do |app|
   app.vm.box = "ubuntu/bionic64"
   app.vm.hostname = "app.local"
   app.vm.network "private_network", ip: "192.168.56.10"
   
   app.vm.provision "shell", inline: <<-SHELL
      SSH_DIR="/home/vagrant/.ssh"
      mkdir -p $SSH_DIR
      chmod 700 $SSH_DIR
      chown  vagrant:vagrant $SSH_DIR

      # Generate key only if it doesn't exist
      if [ ! -f "$SSH_DIR/id_ed25519" ]; then
        sudo -u vagrant ssh-keygen -t ed25519 -f $SSH_DIR/id_ed25519 -N ""
        chmod 600 $SSH_DIR/id_ed25519
       chmod 644 $SSH_DIR/id_ed25519.pub
      fi
      # Copy public key to shared folder
      cp $SSH_DIR/id_ed25519.pub /vagrant/app.pub
    SHELL

    # Second shell provisioner: wait for db.pub and configure authorized_keys
    app.vm.provision "shell", inline: <<-SHELL
      SSH_DIR="/home/vagrant/.ssh"
      AUTH_KEYS="$SSH_DIR/authorized_keys"
      
      while [ ! -f /vagrant/db.pub ]; do
        echo "Waiting for db.pub..."
        sleep 1
      done

      cat /vagrant/db.pub >> $AUTH_KEYS
      chown vagrant:vagrant $AUTH_KEYS
      chmod 600 $AUTH_KEYS
    SHELL
  end

  config.vm.define "db" do |db|
   db.vm.box= "ubuntu/bionic64"
   db.vm.hostname = "db.local"
   db.vm.network "private_network", ip: "192.168.56.11"

   db.vm.provision "shell", inline: <<-SHELL
      SSH_DIR="/home/vagrant/.ssh"
      mkdir -p $SSH_DIR
      chmod 700 $SSH_DIR
      chown  vagrant:vagrant $SSH_DIR

      # Generate key only if it doesn't exist
      if [ ! -f "$SSH_DIR/id_ed25519" ]; then
        sudo -u vagrant ssh-keygen -t ed25519 -f $SSH_DIR/id_ed25519 -N ""
        chmod 600 $SSH_DIR/id_ed25519
        chmod 644 $SSH_DIR/id_ed25519.pub
      fi
        
      # Copy public key to shared folder
      cp $SSH_DIR/id_ed25519.pub /vagrant/db.pub
    SHELL

    # Second provisioner to wait for app.pub and configure authorized_keys
    db.vm.provision "shell", inline: <<-SHELL
      SSH_DIR="/home/vagrant/.ssh"
      AUTH_KEYS="$SSH_DIR/authorized_keys"

      while [ ! -f /vagrant/app.pub ]; do
        echo "Waiting for app.pub..."
        sleep 1
      done

      cat /vagrant/app.pub >> $AUTH_KEYS
      chown vagrant:vagrant $AUTH_KEYS
      chmod 600 $AUTH_KEYS
    SHELL
  end
end