$script = <<SCRIPT#!/bin/bash

if [ ! -f /usr/bin/curl ]; then
  echo "Installing curl"
  sudo apt-get install curl -y
fi
echo "curl:\t$(curl --version)" | head -n 1

if [ ! -f /home/vagrant/VBoxGuestAdditions_4.2.10.iso ]; then
  echo "Upgrading VBoxGuestAdditions"
  curl --location http://download.virtualbox.org/virtualbox/4.2.10/VBoxGuestAdditions_4.2.10.iso > /home/vagrant/VBoxGuestAdditions_4.2.10.iso
  sudo mount /home/vagrant/VBoxGuestAdditions_4.2.10.iso -o loop /mnt
  sudo apt-get install build-essential module-assistant linux-headers-$(uname -r) -y
  sudo sh /mnt/VBoxLinuxAdditions.run --nox11
  echo "=-=-=-=-=-=-=-=-=-=-=-==-=-=-=-=-=-=-=-=-=-=-==-=-=-=-=-=-=-=-=-=-=-="
  echo " VBoxGuestAdditions updated.  Please run: vagrant halt && vagrant up "
  echo "=-=-=-=-=-=-=-=-=-=-=-==-=-=-=-=-=-=-=-=-=-=-==-=-=-=-=-=-=-=-=-=-=-="
  exit 1
fi 

if [[ ! "$(ruby --version)" =~ "ruby 1.9.3" ]]; then
  echo "Upgrading ruby to 1.9.1"
  sudo apt-get install ruby1.9.1 ruby1.9.1-dev \
    rubygems1.9.1 irb1.9.1 ri1.9.1 rdoc1.9.1 \
    build-essential libopenssl-ruby1.9.1 libssl-dev zlib1g-dev \
    libxslt-dev libxml2-dev -y
   
  sudo update-alternatives --install /usr/bin/ruby ruby /usr/bin/ruby1.9.1 400 \
           --slave   /usr/share/man/man1/ruby.1.gz ruby.1.gz \
                          /usr/share/man/man1/ruby1.9.1.1.gz \
          --slave   /usr/bin/ri ri /usr/bin/ri1.9.1 \
          --slave   /usr/bin/irb irb /usr/bin/irb1.9.1 \
          --slave   /usr/bin/rdoc rdoc /usr/bin/rdoc1.9.1
   
  # choose your interpreter
  # changes symlinks for /usr/bin/ruby , /usr/bin/gem
  # /usr/bin/irb, /usr/bin/ri and man (1) ruby
  sudo update-alternatives --config ruby || true
  sudo update-alternatives --config gem  || true

  echo -e "#Ensure gems are in path\nexport PATH=\$PATH:/var/lib/gems/1.9.1/bin/" >> /etc/profile
fi
echo "ruby:\t$(ruby --version)"

if [ ! -f /usr/bin/mysql ]; then
  echo "Installing Mysql"
  sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password password secret_password'
  sudo debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password_again password secret_password'
  sudo apt-get install mysql-server-5.5 -y
fi
echo "mysql:\t$(mysql --version)"

if [ ! -f /usr/bin/git ]; then
  echo "Installing git"
  sudo apt-get install git-core -y
fi
echo "git:\t$(git --version)"

if [[ ! "$(php --version)" =~ "PHP 5.4" ]]; then
  echo "Installing PHP5"
  sudo apt-get install python-software-properties -y
  sudo add-apt-repository ppa:ondrej/php5 -y
  sudo apt-get update
  sudo apt-get install php5 php5-mysql -y
fi
echo "php:\t$(php -v)" | head -n 1

if [ ! -f /usr/bin/hhvm ]; then
  echo "Installing hiphop"
  echo "deb http://dl.hiphop-php.com/ubuntu precise main" | sudo tee -a /etc/apt/sources.list
  sudo apt-get update
  sudo apt-get install hiphop-php -y --force-yes
fi
echo "hhvm:\t$(hhvm --version)" | head -n 1

if [[ ! "$(node --version)" =~ "v0.10" ]]; then
  echo "Installing nodejs"
  sudo apt-get install python-software-properties -y
  sudo add-apt-repository ppa:richarvey/nodejs -y  
  sudo apt-get update
  sudo apt-get install nodejs npm -y
fi
echo "node:\t$(node -v)"

if [ ! -f /usr/bin/whippet ]; then
  echo "Installing whippet"
  sudo git clone https://github.com/dxw/whippet.git /usr/local/whippet
  cd /usr/local/whippet && git submodule update --init
  sudo ln -s /usr/local/whippet/whippet /usr/bin/whippet
fi

if [ ! -f /usr/bin/wp ]; then
  echo "Installing wp-cli"
  sudo curl http://wp-cli.org/packages/phar/wp-cli.phar > /usr/bin/wp
  chmod +x /usr/bin/wp
fi

if [ ! -f /usr/bin/grunt ]; then
  sudo npm install -g grunt-cli
  sudo chown -R vagrant:vagrant /home/vagrant
fi
echo "grunt:\t$(grunt --version)"

if [ ! -f /usr/local/bin/mason ]; then
  sudo gem install mason foreman
  mason buildpacks:install https://github.com/mchung/heroku-buildpack-wordpress.git
fi
echo "mason:\t$(mason --version)"

echo "Clean up..."
sudo apt-get autoremove -y | tail -n 1

echo "=-=-=-=-=-=-=-=-=-=-=-="
echo "Provisioning completed!"
echo "=-=-=-=-=-=-=-=-=-=-=-="
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network :forwarded_port, guest: 4567, host: 4567

  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--memory", "1024"]
  end

  config.vm.provision :shell, :inline => $script
end