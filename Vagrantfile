# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  config.vm.define :fai do |fai|
    fai.vm.box = "debian/bullseye64"
    fai.vm.hostname = "faiserver"
    fai.vm.provider :libvirt do |libvirt|
      libvirt.memory = 2048
    end

#    fai.vm.network "private_network", ip: "192.168.33.2", hostname: true,
#      libvirt__dhcp_enabled: false,
#      libvirt__host_ip: "192.168.33.250",
#      libvirt__network_name: "fai_network"

    # public network
    fai.vm.network :public_network,
      :dev => "br0",
      ip: "192.168.33.250", 
      hostname: true

    fai.vm.provision "shell", inline: <<-SHELL
      # timezone
      timedatectl set-timezone Europe/Budapest
      echo "deb https://fai-project.org/download bullseye koeln" > /etc/apt/sources.list.d/fai.list
      wget -O /etc/apt/trusted.gpg.d/fai-project.gpg https://fai-project.org/download/2BF8D9FE074BCDE4.gpg
      apt-get update
      apt-get upgrade -y
      apt-get install -y fai-quickstart
      cp /usr/share/doc/fai-doc/examples/etc/dhcpd.conf /etc/dhcp/dhcpd.conf
      sed -i 's/INTERFACESv4=""/INTERFACESv4="eth1"/' /etc/default/isc-dhcp-server
      sed -i 's/INTERFACESv6=""/#INTERFACESv6=""/' /etc/default/isc-dhcp-server
      sed -i 's/domain-name-servers 192.168.33.250;/domain-name-servers 8.8.8.8;/' /etc/dhcp/dhcpd.conf
      systemctl restart isc-dhcp-server.service
      sed -i 's/#LOGUSER/LOGUSER/' /etc/fai/fai.conf
      echo "SERVERINTERFACE=\"eth1\"" >> /etc/fai/nfsroot.conf
      echo "NFSROOT_SERVER=\"192.168.33.2\"" >> /etc/fai/nfsroot.conf
      fai-setup -v
      echo '192.168.33.3 demohost' >> /etc/hosts
      dhcp-edit -r demohost
      dhcp-edit demohost 00:11:22:0a:0b:0c 192.168.33.3
      ainsl /srv/fai/nfsroot/etc/fai/fai.conf '^LOGUSER=fai$'
      # sed -i 's/Berlin/Budapest/' /srv/fai/config/class/FAIBASE.var
      ainsl -a /srv/fai/config/class/TIMEZONE.var 'TIMEZONE=Europe/Budapest'
      fai-chboot -IF \
        -u nfs://192.168.33.2/srv/fai/config \
        -k ADDCLASSES=TIMEZONE demohost
      iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
      ainsl /etc/sysctl.conf '^net.ipv4.ip_forward=1'
      sysctl -p
    SHELL
  end

  config.vm.define :demohost, autostart: false do |demohost|
    demohost.vm.network :private_network,
      libvirt__network_name: "fai_network",
      mac: "00:11:22:0a:0b:0c"

    demohost.vm.provider :libvirt do |libvirt|
      libvirt.storage :file,
        size: '5G'
      libvirt.memory = 2048
      # EFI
#      libvirt.loader = "/usr/share/edk2-ovmf/x64/OVMF_CODE.fd"
      boot_network = { 'network' => 'fai_network' }
      libvirt.boot boot_network
      libvirt.boot 'hd'
    end
  end
end
