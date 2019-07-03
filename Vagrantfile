##############################################################################
# All in One Lifecycle Manager Vagrantfile
###############################################################################
Vagrant.configure(2) do |config|
  config.vm.define 'allinone' do |nodeconfig|

    nodeconfig.vm.hostname = 'allinone'
    nodeconfig.vm.box = 'accanto/xenial'
    nodeconfig.vm.box_version = "1.0"
    nodeconfig.vm.box_check_update = false
    nodeconfig.vm.network 'private_network', ip: '192.168.56.100'

    nodeconfig.vm.provider "virtualbox" do |virtualbox|
      virtualbox.name = 'allinone'
      virtualbox.gui = false
      virtualbox.cpus = 4
      virtualbox.memory = 14336
      virtualbox.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
      virtualbox.customize [ "modifyvm", :id, "--natnet1", "192.168.222.0/24"]
    end

    nodeconfig.vm.provider :libvirt do |libvirt, override|
      libvirt.cpus = 4
      libvirt.memory = 14336
      libvirt.nested = true
      libvirt.cpu_mode = 'host-passthrough'
    end

    nodeconfig.vm.provider :hyperkit do |hyperkit, override|
      hyperkit.cpus = 4
      hyperkit.memory = "14G"
      hyperkit.xhyve_binary = "/usr/local/bin/hyperkit"
      hyperkit.kernel_command = "earlyprintk=serial root=/dev/vda1 rw acpi=off console=ttyS0 LANG=en_GB.UTF-8"
      hyperkit.uuid = "8e7af180-c54d-4aa2-9bef-59d94a1ac575"

      override.vm.box = "accanto/hyperkit"
      # Hyperkit does not allow us to explicitly set the IP address of a guest machine but does allow us to
      # fix it to an IP address generated from a UUID.
      override.vm.network 'private_network', ip: '192.168.65.2'
      override.vm.synced_folder './', '/vagrant', type: 'rsync'
    end

    nodeconfig.vm.provision "ansible_local" do |ansible|
      ansible.become = true
      ansible.playbook = "ansible/start-aio.yml"
    end
  end
end
