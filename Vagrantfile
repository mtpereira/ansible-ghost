boxes = {
  "ghost-debian-wheezy" => {
    :box => "boxcutter/debian8",
    :ip  => '10.0.21.2',
    :cpu => "100",
    :ram => "512"
  },
  "ghost-ubuntu-trusty" => {
    :box => "boxcutter/ubuntu1404",
    :ip  => '10.0.21.3',
    :cpu => "100",
    :ram => "512"
  },
}

Vagrant.configure("2") do |config|

  if Vagrant.has_plugin? "vagrant-vbguest"
    config.vbguest.auto_update = true
  end

  boxes.each do |box_name, box|
    config.vm.define box_name do |machine|
      machine.vm.box = box[:box]
      machine.vm.box_url = box[:url]
      machine.vm.hostname = "%s" % box_name

      machine.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--cpuexecutioncap", box[:cpu]]
        v.customize ["modifyvm", :id, "--memory",          box[:ram]]
      end

      machine.vm.network :private_network, ip: box[:ip]

      machine.vm.provision :shell do |shell|
        shell.inline = "sed -i -e 's/%sudo\tALL=NOPASSWD:ALL/%sudo\tALL=(ALL:ALL) ALL/' /etc/sudoers"
      end

      machine.vm.provision :ansible do |ansible|
        ansible.playbook = "tests/test.yml"
        ansible.verbose = ENV['ANSIBLE_VERBOSE'] ||= "vvvv"
        ansible.tags = ENV['ANSIBLE_TAGS'] ||= "all"
      end
    end
  end
end

#  vim: set ft=ruby ts=2 sw=2 tw=0 et :
