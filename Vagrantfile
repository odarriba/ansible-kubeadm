$num_instances = 4
$instance_name_prefix = "kubernetes"
$subnet = "172.17.8"

host_vars = {}

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  config.vm.box = "debian/stretch64"

  (1..$num_instances).each do |i|
    config.vm.define vm_name = "%s-%02d" % [$instance_name_prefix, i] do |config|
      config.vm.hostname = vm_name

      config.vm.provider "virtualbox" do |v|
        v.memory = 1536
        v.cpus = 2
      end

      ip = "#{$subnet}.#{i+100}"
      host_vars[vm_name] = {
        "ip": ip
      }

      config.vm.network :private_network, ip: ip, mac: "0800278dc04#{i.to_s(16)}"
      config.vm.post_up_message = "#{vm_name} - #{ip}"
    end
  end
end
