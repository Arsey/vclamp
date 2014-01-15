# encoding: utf-8

$server_root_password = "password"

Vagrant.configure("2") do |config|

  config.vm.box = "opscode-ubuntu-12.04_chef-11.4.0"
  config.vm.box_url = "https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_ubuntu-12.04_chef-11.4.0.box"
  config.vm.synced_folder "www", "/var/www", create:true

  config.vm.network "private_network", ip: "11.11.11.11"
  config.vm.network :forwarded_port, guest: 3306, host: 8010
  
  config.ssh.forward_agent = true

  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ["cookbooks"]
    chef.add_recipe :apt
    chef.add_recipe 'mysql::server'
	chef.add_recipe 'mysql::client'
    chef.add_recipe 'php'
	chef.add_recipe 'php::module_mysql'
	chef.add_recipe 'php::module_curl'
    chef.add_recipe 'apache2'
	chef.add_recipe 'apache2::mod_php5'
	chef.add_recipe 'apache_vhosts'
    chef.json = {
		:apache_vhosts=>[
		{
			:app=>"example",
			:path=>"/path/to/example/project/relative/to/synced/guest/folder/or/not",
			:server_name=>"example.local",
			:server_email=>"someemail@email.com",
		}
		],
      :mysql  => {
        :server_root_password   => $server_root_password,
        :server_repl_password   => $server_root_password,
        :server_debian_password => $server_root_password,
        :service_name           => "mysql",
        :basedir                => "/usr",
        :data_dir               => "/var/lib/mysql",
        :root_group             => "root",
        :mysqladmin_bin         => "/usr/bin/mysqladmin",
        :mysql_bin              => "/usr/bin/mysql",
        :conf_dir               => "/etc/mysql",
        :confd_dir              => "/etc/mysql/conf.d",
        :socket                 => "/var/run/mysqld/mysqld.sock",
        :pid_file               => "/var/run/mysqld/mysqld.pid",
        :grants_path            => "/etc/mysql/grants.sql",
		:bind_address => '0.0.0.0'
      },
      :apache => {
        :default_site_enabled => "true",
        :dir                  => "/etc/apache2",
        :log_dir              => "/var/log/apache2",
        :error_log            => "error.log",
        :user                 => "www-data",
        :group                => "www-data",
        :binary               => "/usr/sbin/apache2",
        :cache_dir            => "/var/cache/apache2",
        :pid_file             => "/var/run/apache2.pid",
        :lib_dir              => "/usr/lib/apache2",
        :listen_ports         => [
          "80"
        ],
        :contact              => "ops@example.com",
        :timeout              => "300",
        :keepalive            => "On",
        :keepaliverequests    => "100",
        :keepalivetimeout     => "5"
      }
	 
    }
  end
  
   # configure default users on mysql
  config.vm.provision :shell, :inline => "mysql -u root -p#{$server_root_password} -e \"create database if not exists example_db\""
  config.vm.provision :shell, :inline => "mysql -u root -p#{$server_root_password} -e \"GRANT ALL ON *.* TO 'root'@'10.0.2.2' IDENTIFIED BY 'password'; FLUSH PRIVILEGES;\""
  config.vm.provision :shell, :inline => "sudo service mysql restart"
  
  
end
