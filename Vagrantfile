Vagrant.configure("2") do |config|
    # Configuración base para todas las máquinas
    config.vm.provider "virtualbox" do |vb|
      vb.memory = "512"   # Asignar 512 MB de RAM
      vb.cpus = 1         # Asignar 1 CPU
    end
  
    # Crear el servidor web 1 (Apache)
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/bionic64" # Usar Ubuntu 18.04 como base
      web1.vm.network "private_network", ip: "192.168.33.10" # IP estática
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
      SHELL
      web1.vm.synced_folder "src/web1", "/var/www/html", type: "virtualbox" # Directorio compartido
    end
  
    # Crear el servidor web 2 (Apache)
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/bionic64"
      web2.vm.network "private_network", ip: "192.168.33.11" # IP estática
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
      SHELL
      web2.vm.synced_folder "src/web2", "/var/www/html", type: "virtualbox" # Directorio compartido
    end
  
    # Crear el servidor de Nginx (Balanceador de carga)
    config.vm.define "nginx" do |nginx|
      nginx.vm.box = "ubuntu/bionic64"
      nginx.vm.network "private_network", ip: "192.168.33.12" # IP estática
      nginx.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y nginx
        sudo systemctl enable nginx
        sudo systemctl start nginx
  
        # Configurar Nginx para balanceo de carga
        echo 'upstream backend {' | sudo tee /etc/nginx/sites-available/default > /dev/null
        echo '    server 192.168.33.10;' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo '    server 192.168.33.11;' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo '}' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo 'server {' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo '    listen 80;' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo '    location / {' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo '        proxy_pass http://backend;' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo '    }' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        echo '}' | sudo tee -a /etc/nginx/sites-available/default > /dev/null
        sudo systemctl restart nginx
      SHELL
    end
  end
  