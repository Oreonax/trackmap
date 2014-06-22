# -*- mode: ruby -*-
# vi: set ft=ruby :

#if ARGV.length != 2
#  puts "Error, missing email address as last argument"
#  puts "vagrant up your@email.addr"
#else
#  mail = ARGV.pop
#  puts "using mail: " + mail
#end


Vagrant.configure("2") do |config|

  config.vm.hostname = "supporter"
  config.vm.box = "precise32"
  config.vm.box_url = "http://files.vagrantup.com/precise32.box"
  config.vm.network :public_network

end

$script = <<SCRIPT
date > /etc/vagrant_provisioned_at

echo "Installing dependencies"


apt-get update -y
apt-get install git wget traceroute python-pip gcc python-dev libgeoip-dev geoip-database tor -y 
aptitude remove phantomjs -y
pip install GeoIP tldextract

wget https://phantomjs.googlecode.com/files/phantomjs-1.9.2-linux-i686.tar.bz2 -o /tmp/wget.log


cat > sha224.check << EOF
4b6156fcc49dddbe375ffb8895a0003a4930aa7772f9a41908ac611d  phantomjs-1.9.2-linux-i686.tar.bz2
EOF

sha224sum -c sha224.check
if [ $? != "0" ]; then
    echo "OMG checksum fail!? quit+pleae email: vecna [at] globaleaks [:] org"
    init 0
fi

tar jxf phantomjs-1.9.2-linux-i686.tar.bz2 
cd phantomjs-1.9.2-linux-i686/bin
ln -s `pwd`/phantomjs /usr/bin/phantomjs
cd ~

git clone https://github.com/vecna/helpagainsttrack.git
mkdir hs

cat > /etc/tor/torrc << EOF
HiddenServiceDir /home/vagrant/hs
HiddenServicePort 22 127.0.0.1:22
EOF

service tor restart


# c7:1c:c4:df:02:ca:a8:27:fb:8d:33:de:14:90:d8:69 qq@qq
# The key's randomart image is:
# +--[ RSA 4160]----+
# |         ..      |
# |     o o .o      |
# |    . Eo ..o .   |
# |     ...oo .o .  |
# |     .  S +  .   |
# |    o .  o       |
# |     +  .        |
# |    . o=         |
# |     o+oo        |
# +-----------------+
# 

if [ ! -d /root/.ssh ]; then
    mkdir /root/.ssh
fi

cat > /root/.ssh/authorized_keys << EOF
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACCQDXs4GKO11jVef9U4DMFV8+IlJn2Qz8DtHA251tBXmNknuzpdF922h9xwzwr/rLvZ0QUfZdS5ijG0YUbMQG1SaiJbMAaijVYW/2bjZ3eYx+/LKEsTE92+ddFuxOAROxRPyVh0Iehzl6/cJLoGrKPM0jmRixCvxsusyc2LUVzUoPm+My815kzIk68dmscFEUdgx1JcLyt2w4V7oCAIy5hwEuwwUn2v8WkYGc8h/SNXV3elH33M/RiRGk8FPuuLjlUkAnZa/SOts33gkvH0YHrkc3RRIMDSHxlkjFdOkw1CVVOAnFHXR1BKL6Euoidi8RyyY2ihHfPATz0AVclfjkzZlcev1Kcyxtvj2iBX2vpBM0+GzHWhdRc7sKLO1YJbYP7NHu+VWK+jg2Lvd5Rx/gxP3i23F8sAc2gfYGEZuldU8ac6leCw+7B/s03pPC90+Q2zUYDsoMe4IAGNcmM8gMQaRNSPbEA3x5CYjIEUeEHGGCSlyP5mFbcTQnlIpixcqcJp7EGPpTzs3GyPowkCo+N+0ZlIQlfEVis7vFjXKRa7f0P5a9a+NQA6tibGGXBHKQFdrB3l9qHwTpb6ZvjXJg0oVSs0Uk/u46ggcja99FFOQ60jpAfugHR4ilofskMKDW7mUK6tD88ufxovWq7qDCTmwgbbz/HsmJlt7l2ZtuKv6V7OBvlyMGph8r You!SearchOnYoutubeDakhDaughterThyAreAwezume
EOF
# rm /home/vagrant/.ssh/authorized_keys

SCRIPT

Vagrant.configure("2") do |config|

  mailaddr = ENV["mail"] 
  if mailaddr.nil?
     puts "Missing email address environment variable: please use 'export mail=\"mail@youraddr\"'"
     exit
  end

  config.vm.provision "shell" do |s|
    s.inline = "echo $1 > $2 "
    s.args   = [ mailaddr, "mailaddr" ]
  end

  # config.vm.provision "shell", inline: $script
end
