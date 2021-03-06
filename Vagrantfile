default_box_url = "/home/thorsten/.vagrant.d/boxes/froschi-precise64-base.box"

storage_boxes = {
  :storage1 => {
    :url => default_box_url,
    :ip => "192.168.1.10",
  },
  :storage2 => {
    :url => default_box_url,
    :ip => "192.168.1.11",
  },
}

require './lib/hd_provisioner.rb'

Vagrant::Config.run do |config|

  storage_boxes.each_pair do |name, options|

    config.vm.define name do |box|
      # Some metadata
      box.vm.box = name.to_s
      box.vm.host_name = name.to_s
      box.vm.box_url = options[:url]

      # Use this when debugging
      #box.vm.boot_mode = :gui

      # Hardware configuration
      box.vm.customize ["modifyvm", :id, "--memory", 256]
      box.vm.customize ["modifyvm", :id, "--cpus", 1]
      box.vm.customize ["modifyvm", :id, "--name", name]

      # Disable vbguest
      box.vbguest.auto_update = false
      box.vbguest.no_remote = true

      # Networking, in addition to the bridged interface
      box.vm.network :hostonly, options[:ip]

      # Test.
      box.vm.provision HDProvisioner

      # Provisioning
      box.vm.provision :chef_solo do |chef|
        chef.cookbooks_path = "cookbooks"
        chef.add_recipe("prerequisites")
        chef.add_recipe("drbd")

        chef.json = {
          :stuff => name
        }
      end
    end

  end

end
