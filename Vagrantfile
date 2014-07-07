Vagrant.configure('2') do |config|
  box_name = 'precise-server-cloudimg-vagrant-amd64'
  box_url = 'http://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-vagrant-amd64-disk1.box'

  server_list = {
    'nginx-dev-1' => {
      :ip => '33.33.66.10',
      :runlist => ['nginx']
    }
  }
  
  cookbooks_path = 'cookbooks'


  [:up, :provision].each do |cmd|
    config.trigger.before cmd, :stdout => true do
      info 'Cleaning cookbook directory'
      run "rm -rf #{cookbooks_path}"
      info 'Installing cookbook dependencies with berkshelf'
      run "berks vendor #{cookbooks_path}"
    end
  end

  server_list.each do |server_name, options| 
    config.vm.define server_name do |server|
      server.vm.box = box_name
      server.vm.box_url = box_url
      server.vm.hostname = server_name
      server.vm.network :private_network, ip: options[:ip]
      server.vm.provision :shell, :inline => 'dpkg -s chef || curl -L https://www.opscode.com/chef/install.sh | bash -s'
      server.vm.provider 'virtualbox' do |v|
        v.customize ['modifyvm', :id, '--memory', '2048']
      end
      server.vm.provision :chef_solo do |chef|
        chef.run_list = options[:runlist]
      end
    end
  end
end
