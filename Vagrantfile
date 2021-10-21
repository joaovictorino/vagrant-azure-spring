$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql < /vagrant/mysql/script/user.sql && \
  mysql < /vagrant/mysql/script/schema.sql && \
  mysql < /vagrant/mysql/script/data.sql && \
  cat /vagrant/mysql/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf && \
  service mysql restart
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "azure"

  config.ssh.private_key_path = "ssh/key"

  config.vm.provider "azure" do |azure, override|
    azure.tenant_id = "57294900-c35e-4948-91fa-ebdf90d3427e"
    azure.client_id = "919dcf33-8183-4411-95b6-acb308e8c4d1"
    azure.client_secret = "R.WgQz..J2Z0Ta2ctJel0KSluSwBaA5cpY"
    azure.subscription_id = "3231ca8c-f392-4473-b23d-ef052e9eed0f"
    azure.vm_image_urn = "Canonical:UbuntuServer:18.04-LTS:latest"
    azure.location = "westus"
    azure.resource_group_name = 'vagrant'
  end

  config.vm.define "mysqlserver", primary: true do |mysqlserver|

    mysqlserver.vm.provider "azure" do |azure|
      azure.vm_name = "mysqlserver"
    end
    
    mysqlserver.vm.provision "shell", inline: $script_mysql
  end

  config.vm.define "springapp" do |springapp|

    springapp.vm.provider "azure" do |azure|
      azure.vm_name = "springapp"
      azure.tcp_endpoints = "8080"
    end

    springapp.vm.provision "shell", inline: "apt-get update && apt-get install -y openjdk-11-jre unzip"
    springapp.vm.provision "shell", inline: "unzip -o /vagrant/springapp/springapp.zip -d /srv"
    springapp.vm.provision "shell", inline: "mkdir -p /var/log/springapp"
    springapp.vm.provision "shell", inline: "cp /vagrant/springapp/springapp.service /etc/systemd/system/springapp.service"
    springapp.vm.provision "shell", inline: "sudo systemctl start springapp.service"
    springapp.vm.provision "shell", inline: "sudo systemctl enable springapp.service"
  end
end