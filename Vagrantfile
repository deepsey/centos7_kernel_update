# Describe VMs
#home = ENV['HOME']
MACHINES = {
  # VM name "kernel update"
  :"centos7-kernel-update" => {
              # VM box
              :box_name => "centos/7",
              # IP address
              :ip_addr => '192.168.100.4',
              # VM CPU count
              :cpus => 12,
              # VM RAM size (Mb)
              :memory => 32768,
              # forwarded ports
              :forwarded_port => []
              #:script_sh => "otus-selinux.sh" 
  }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
    # Apply VM config
    config.vm.define boxname do |box|
      # Set VM base box and hostname
      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s
      box.vm.box_check_update = false
      #box.vm.provision "shell", path: boxconfig[:script_sh]
      #box.vm.box_url = boxconfig[:box_url]
      #box.vm.box_download_checksum = boxconfig[:box_download_checksum]
      #box.vm.box_download_checksum_type = boxconfig[:box_download_checksum_type]
      box.vm.network "private_network", ip: boxconfig[:ip_addr]
      # Port-forward config if present
      if boxconfig.key?(:forwarded_port)
        boxconfig[:forwarded_port].each do |port|
          box.vm.network "forwarded_port", port
        end
      end
      # VM resources config
      
      box.vm.provider "virtualbox" do |v|
        # Set VM RAM size and CPU count
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
        v.name = boxname.to_s
      end
      
    end
 
  end
  
end
