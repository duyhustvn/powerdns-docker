Vagrant.configure("2") do |config|
  # Define VM configurations
  vms = [
    { gui_display_name: "powerdns01", name: "powerdns01", hostname: "powerdns01", ip: "192.168.56.53" },
  ]

  vms.each do |vm|

    config.vm.define vm[:name] do |vm_config|
      vm_config.vm.box = "ubuntu/jammy64" # Specify the box you want to use
      # vm_config.vm.synced_folder "./rke2-artifacts", "/root/rke2-artifacts", type: "rsync"
      #
      # host name
      # vm_config.vm.hostname = vm[:hostname]
      # Fixed IP addresses for private network
      vm_config.vm.network "private_network", ip: vm[:ip]

      vm_config.vm.provider "virtualbox" do |vb|
        vb.name = vm[:gui_display_name]
        vb.memory = "4096"
        vb.cpus = 4
      end

      # Build the hosts string
      # hosts_entries = vms.map { |other_vm| "#{other_vm[:ip]} #{other_vm[:hostname]}" unless other_vm[:hostname] == vm[:hostname] }.compact.join("\n")
      hosts_entries = vms.map { |other_vm| "#{other_vm[:ip]} #{other_vm[:hostname]}" }.join("\n")


      # Provision the VM with the hosts entries
      vm_config.vm.provision "shell", inline: <<-SHELL
        # Set hostname
        hostnamectl set-hostname #{vm[:hostname]}
        echo "127.0.0.1 #{vm[:hostname]}" >> /etc/hosts
        echo "#{hosts_entries}" >> /etc/hosts

        # Add SSH public key to authorized keys
        # Remember to generate your own SSH key pair 
        echo "#{File.read(File.expand_path('~/.ssh/id_ed25519.pub'))}" >> /home/vagrant/.ssh/authorized_keys

        # Change permission of authorized key
        chmod 600 /home/vagrant/.ssh/authorized_keys
        chown vagrant:vagrant /home/vagrant/.ssh/authorized_keys
      SHELL

    end
  end
end
