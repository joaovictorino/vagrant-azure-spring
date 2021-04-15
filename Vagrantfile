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
    azure.tenant_id = ""
    azure.client_id = ""
    azure.client_secret = ""
    azure.subscription_id = ""
    azure.vm_image_urn = "Canonical:UbuntuServer:18.04-LTS:latest"
    azure.location = "westus"
    azure.resource_group_name = 'vagrant'
  end

  config.vm.define "mysqlserver" do |mysqlserver|

    mysqlserver.vm.provider "azure" do |azure|
      azure.vm_name = "mysqlserver"
    end
    
    mysqlserver.vm.provision "shell", inline: $script_mysql
  end

  config.vm.define "springapp" do |springapp|

    springapp.vm.provider "azure" do |azure|
      azure.vm_name = "springapp"
      azure.tcp_endpoints = "80"
    end

    springapp.vm.provision "shell", inline: "apt-get update && apt-get install -y openjdk-11-jre unzip"
    springapp.vm.provision "shell", inline: "unzip -o /vagrant/springapp/springapp.zip -d /srv"
    springapp.vm.provision "shell", inline: "sudo nohup java -Dspring.profiles.active=mysql -jar /srv/*.jar --server.port=80 &"
    springapp.vm.provision "shell", inline: "sleep 30"
  end
end