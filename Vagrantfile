Vagrant::Config.run do |config|
  config.vm.box = "DebianSqueeze64"
  config.vm.box_url = "https://dl.dropbox.com/u/1543052/Boxes/DebianSqueeze64.box"
  config.vm.network :hostonly, '192.168.3.14'

  # Default user/group id for vagrant in Debian
  host_user_id = 1000
  host_group_id = 1000

  if RUBY_PLATFORM =~ /linux|darwin/
    config.vm.share_folder "vagrant-root", "/vagrant", "."#, :nfs => true
    host_user_id = Process.euid
    host_group_id = Process.egid
  end

  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ['cookbooks', 'site-cookbooks']

    chef.add_recipe('rvm::vagrant')
    chef.add_recipe('rvm::user')

    chef.add_recipe('postgresql::server')
    chef.add_recipe('postgresql::ruby')

    chef.add_recipe('database::postgresql')

    chef.add_recipe('phantomjs')

    # This is where all the magic happens.
    # see site-cookbooks/gitlab/
    chef.add_recipe('gitlab::vagrant')

    chef.binary_path = '/var/lib/gems/1.8/bin'

    chef.json = {
      :rvm => {
        :user_installs => [
          { :user         => 'vagrant',
            :default_ruby => '2.0.0'
          }
        ],
        :vagrant => {
          :system_chef_solo => chef.binary_path
        },
        :global_gems => [{ :name => 'bundler'}],
        :branch => 'none',
        :version => '1.17.10'
      },
      :phantomjs => {
        :version => '1.8.1'
      },
      :gitlab => {
        :host_user_id => host_user_id,
        :host_group_id => host_group_id
      }
    }
  end
end

Vagrant.configure("2") do |config|
    config.vm.provider :vmware_fusion do |v, override|
        override.vm.box="precise64"
        override.vm.box_url = "http://files.vagrantup.com/precise64_vmware.box"
        v.vm["memsize"] = "1024"
    end

    config.vm.provider :virtualbox do |v, override|
        v.customize ["modifyvm", :id, "--memory", 1024]
    end
end

