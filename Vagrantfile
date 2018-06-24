# -*- mode: ruby -*-
# vi: set ft=ruby :

# using dotenv
Dotenv.load!(".env")

Vagrant.configure("2") do |config|
  config.vm.box = "dummy"
  config.vm.provider :aws do |aws, override|
    override.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
    aws.access_key_id     = ENV['AWS_ACCESS_KEY_ID']
    aws.secret_access_key = ENV['AWS_SECRET_ACCESS_KEY']
    aws.keypair_name      = ENV['AWS_KEYPAIR_NAME']
    
    override.ssh.username         = ENV['AWS_SSH_USERNAME']
    override.ssh.private_key_path = ENV['AWS_SSH_KEY']
    
    aws.region            = ENV['AWS_REGION']
    aws.availability_zone = ENV['AWS_AZ']
    aws.subnet_id         = ENV['AWS_SUBNET_ID']
    aws.security_groups   = ENV['AWS_SECURITY_GROUPS'].split(',').map(&:strip)

    # aws.ami = "ami-92df37ed"   # Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type
    aws.ami = "ami-2724cf58"    # Amazon Linux 2 LTS Candidate 2 AMI (HVM), SSD Volume Type
    aws.instance_type = "t2.nano"
    aws.tags = {
      # 'Name' => "test"
    }

    aws.user_data = <<~__USER_DATA__
    #!/bin/sh
    sed -i -e 's/^\\(Defaults.*requiretty\\)/#\\1/' /etc/sudoers
    __USER_DATA__

    # Synced Folders ###################################################
    # config.vm.synced_folder ".", "/vagrant", type: "rsync"
    config.vm.synced_folder ".", "/vagrant", type: "rsync", disabled: true
    
    # Plug-in ----------------------------------------------------------

    # [plugin] vagrant-vbguest
    if Vagrant.has_plugin?("vagrant-vbguest") then
      config.vbguest.auto_update = true   # fail on "bento/centos-7.2"
    end

    # [plugin] Vagrant Host Manager
    if Vagrant.has_plugin?("vagrant-manager") then
      config.hostmanager.enabled            = true
      config.hostmanager.manage_host        = true
      config.hostmanager.manage_guest       = true
      config.hostmanager.ignore_private_ip  = false
      config.hostmanager.include_offline    = true
    end

    # Provisioning #####################################################

    config.vm.provision "shell", inline: <<-SHELL
      # For development
      # sudo yum -y install epel-release
      # sudo yum -y update
      # sudo yum -y groupinstall 'Development Tools'
      # yum install -y readline-devel
    SHELL

    if Vagrant.has_plugin?("ansible") then
      config.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbook.yml"
      end
    end

  end

end

