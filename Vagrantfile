# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# vagrant 1.5.4 has working tags for config.vm.provision
Vagrant.require_version ">= 1.5.4"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.ssh.private_key_path = "workshop.key"

  config.vm.define :desktop do |desktop|

    desktop.ssh.username = "vagrant"

    desktop.vm.box       = "workshop-desktop"
    desktop.vm.box_url   = "packer_desktop_virtualbox.box"
    desktop.vm.hostname  = :desktop

    desktop.vm.provider  :virtualbox do |vb|
      vb.gui = true
      vb.customize [
        'storageattach', :id,
        '--storagectl',  'SATA Controller',
        '--port',        1,
        '--device',      0,
        '--type',        'dvddrive',
        '--medium',      'd:/isos/CentOS-7.0-1406-x86_64-DVD.iso'
      ]
    end

    # a file provisioner
    desktop.vm.provision "file", source: "c7-media.repo", destination: "~/c7-media.repo"

    # an inline script provisioner using a multiline script varialbe
    $script = <<MEDIAREPO
      set -x
      cp /home/vagrant/c7-media.repo /etc/yum.repos.d
MEDIAREPO

    desktop.vm.provision "shell",
      privileged: true,
      inline: $script

    # a shell script provisioner from a local script with arguments
    desktop.vm.provision "shell",
      privileged: true,
      path: "scripts/mediarepo.sh",
      args: "/dev/sr0"

  end # :desktop


  config.vm.define :aws do |aws|

    aws.ssh.username         = "centos"
    aws.ssh.pty              = true

    aws.vm.box               = "workshop-aws"
    aws.vm.box_url           = "packer_aws_aws.box"

    aws.vm.provider :aws do |aws, override|

      aws.access_key_id     = ENV['AWS_ACCESS_KEY']
      aws.secret_access_key = ENV['AWS_SECRET_KEY']

      aws.ami               = "ami-371f5b0d"
      aws.region            = "ap-southeast-2"
      aws.instance_type     = "t2.micro"
      aws.security_groups   = [ "workshop" ]

      aws.keypair_name      = "workshop.key"

      aws.block_device_mapping = [ {
        :DeviceName => "/dev/sda1",
        :VirtualName => "ebs",
        'Ebs.DeleteOnTermination' => true
        } ]
    end

   config.vm.provision "shell",
      privileged: true,
      inline: "yum install -y bind-utils"
 
  end
end