Vagrant.require_version ">= 1.5"

Vagrant.configure("2") do |config|

    config.vm.box = "ubuntu/trusty64"
    config.vm.network :private_network, ip: "192.168.33.50"
    config.vm.synced_folder "./", "/vagrant", type: "nfs"
    config.ssh.forward_agent = true

    # Assing 1/4 of memory and all processors for virtual machine
    config.vm.provider :virtualbox do |v|


        host = RbConfig::CONFIG['host_os']

        # Give VM 1/4 system memory & access to all cpu cores on the host
        if host =~ /darwin/
            cpus = `sysctl -n hw.ncpu`.to_i
            # sysctl returns Bytes and we need to convert to MB
            mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 4
        elsif host =~ /linux/
            cpus = `nproc`.to_i
            # meminfo shows KB and we need to convert to MB
            mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
        else # sorry Windows folks, I can't help you
            cpus = 2
            mem = 2048
        end

        v.name = "blog.jekyll"
        v.customize ["modifyvm", :id, "--memory", mem]
        v.customize ["modifyvm", :id, "--cpus", cpus]
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end

    # Ansible provisioning (you need to have ansible installed)
    host = RbConfig::CONFIG['host_os']
    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/vagrant_playbook.yml"
        ansible.inventory_path = "ansible/inventories/dev"
        ansible.limit = 'all'
    end

end
