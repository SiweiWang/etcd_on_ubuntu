# -*- mode: ruby -*-
# vi: set ft=ruby :

# Specify Vagrant version, Vagrant API version, and Vagrant clone location
Vagrant.require_version ">= 1.6.0"
VAGRANTFILE_API_VERSION = "2"
ENV["LC_ALL"] = "en_US.UTF-8"
ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'


# Require 'yaml' module
require 'yaml'

# Read YAML file with VM details (box, CPU, RAM, IP addresses)
# Be sure to edit servers.yml to provide correct IP addresses
servers = YAML.load_file(File.join(File.dirname(__FILE__), 'servers.yml'))


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Always use Vagrant's default insecure key
  config.ssh.insert_key = false

  # Iterate through entries in YAML file to create VMs
  servers.each do |server|
    config.vm.define server["name"] do |srv|

      # Don't check for box updates
      srv.vm.box_check_update = false
      srv.vm.hostname = server["name"]
      srv.vm.box = server["box"]

      # Assign an additional static private network
      srv.vm.network "private_network", ip: server["priv_ip"]

      # Specify default synced folder; requires VMware Tools
      # Note shared folders are REQUIRED for the shell provisioning to work
      srv.vm.synced_folder ".", "/vagrant"

      # Provision etcd to the VMs
      srv.vm.provision "shell", path: server["path"], privileged: true

      # Configure VMs with RAM and CPUs per settings in server.yml (VirtualBox)
      srv.vm.provider :virtualbox do |vb|
        vb.memory = server["ram"]
        vb.cpus = server["vcpu"]
      end
    end
  end
end