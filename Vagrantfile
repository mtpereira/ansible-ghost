# -*- mode: ruby -*-
# vim: set ft=ruby ts=2 sw=2 tw=0 et :

boxes = {
  "ghost-debian-wheezy" => {
                        :box => "opscode-debian-7.8",
                        :url => "https://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_debian-7.8_chef-provisionerless.box",
                        :ip  => '10.0.21.2',
                        :cpu => "100",
                        :ram => "128"
                      },
  "ghost-ubuntu-trusty" => {
                        :box => "opscode-ubuntu-14.04",
                        :url => "https://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-14.04_chef-provisionerless.box",
                        :ip  => '10.0.21.3',
                        :cpu => "100",
                        :ram => "128"
                      },
}

Vagrant.configure("2") do |config|
  # add our vagrant private ssh key if it exists
  vagrant_key = File.expand_path('~') + '/.ssh/vagrant'
  vagrant_key_pub = File.expand_path('~') + '/.ssh/vagrant.pub'
  if FileTest.exists?(vagrant_key) and FileTest.exists?(vagrant_key_pub)
    config.ssh.private_key_path = [vagrant_key, "~/.vagrant.d/insecure_private_key"]
    config.ssh.insert_key = false
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

      # add our vagrant private key
      if config.ssh.private_key_path.kind_of?(Array) and
         config.ssh.private_key_path.include? vagrant_key
        machine.vm.provision :shell do |shell|
          vagrant_key_pub_content = File.read(vagrant_key_pub)
          shell.inline = "echo '#{vagrant_key_pub_content}' > /home/vagrant/.ssh/authorized_keys && chmod 600 /home/vagrant/.ssh/authorized_keys"
        end
      end

      machine.vm.provision :ansible do |ansible|
        ansible.playbook = "test.yml"
        ansible.verbose = ENV['ANSIBLE_VERBOSE'] ||= "v"
      end
    end
  end
end

