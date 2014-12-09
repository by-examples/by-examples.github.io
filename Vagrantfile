VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.box = "jekyll-v0.1.0"

    config.vm.hostname = "by-examples.lh"

    require 'ffi'

    if FFI::Platform::IS_WINDOWS
        print "\n\n   ===> win\n\n"
        config.vm.synced_folder ".", "/vagrant", :nfs => false
        config.vm.network :forwarded_port, guest: 80, host: 8880, host_ip: "127.0.0.1"

    else
        print "\n\n   ===> not win\n\n"
        config.vm.network "private_network", ip: "192.168.200.210"
        #config.vm.network :forwarded_port, guest: 80, host: 8880, host_ip: "127.0.0.1"
        #config.vm.synced_folder ".", "/vagrant", :nfs => true
    end


end
