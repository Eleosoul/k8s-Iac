require 'fileutils'

# Функция для загрузки переменных из .env
def load_env(file)
  return {} unless File.exist?(file)
  env = {}
  File.readlines(file).each do |line|
    key, value = line.strip.split('=', 2)
    next if key.nil? || value.nil?
    env[key] = value.gsub(/"/, '')  # Убираем кавычки
  end
  env
end

ENV_VARS = load_env(".env")  # Загружаем переменные

Vagrant.configure("2") do |config|
  # Настройки мастер-ноды
  config.vm.define "master" do |master|
    master.vm.box = ENV_VARS['MASTER_BOX']
    master.vm.hostname = ENV_VARS['MASTER_HOSTNAME']
    master.vm.network "public_network", bridge: ENV_VARS['NETWORK_ADAPTER'], ip: ENV_VARS['MASTER_IP']

    master.vm.provider "virtualbox" do |vb|
      vb.memory = ENV_VARS['MASTER_MEMORY']
      vb.cpus = ENV_VARS['MASTER_CPUS']
      vb.customize ["createhd", "--filename", "./hdds/master_disk.vdi", "--size", 102400]
      vb.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 1, "--device", 0, "--type", "hdd", "--medium", "./hdds/master_disk.vdi"]
    end

    master.vm.provision "shell", inline: <<-SHELL
      mkdir -p /home/vagrant/.ssh
      echo "#{ENV_VARS['SSH_KEY_MAC']}" >> /root/.ssh/authorized_keys
      echo "#{ENV_VARS['SSH_KEY_HOME']}" >> /root/.ssh/authorized_keys
  
      parted /dev/sdb mklabel gpt
      parted /dev/sdb mkpart primary ext4 0% 60GB   # /var
      parted /dev/sdb mkpart primary ext4 60GB 80GB # /opt
      parted /dev/sdb mkpart primary ext4 80GB 100GB # /tmp
  
      mkfs.ext4 /dev/sdb1
      mkfs.ext4 /dev/sdb2
      mkfs.ext4 /dev/sdb3
  
      mkdir -p /mnt/var /mnt/opt /mnt/tmp
      mount /dev/sdb1 /mnt/var
      mount /dev/sdb2 /mnt/opt
      mount /dev/sdb3 /mnt/tmp
  
      echo '/dev/sdb1 /var ext4 defaults 0 2' >> /etc/fstab
      echo '/dev/sdb2 /opt ext4 defaults 0 2' >> /etc/fstab
      echo '/dev/sdb3 /tmp ext4 defaults 0 2' >> /etc/fstab
  SHELL
  end

  # Настройки воркер-ноды
  config.vm.define "worker" do |worker|
    worker.vm.box = ENV_VARS['WORKER_BOX']
    worker.vm.hostname = ENV_VARS['WORKER_HOSTNAME']
    worker.vm.network "public_network", bridge: ENV_VARS['NETWORK_ADAPTER'], ip: ENV_VARS['WORKER_IP']

    worker.vm.provider "virtualbox" do |vb|
      vb.memory = ENV_VARS['WORKER_MEMORY']
      vb.cpus = ENV_VARS['WORKER_CPUS']
      vb.customize ["createhd", "--filename", "./hdds/worker_disk.vdi", "--size", 102400]
      vb.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 1, "--device", 0, "--type", "hdd", "--medium", "./hdds/worker_disk.vdi"]
    end

    worker.vm.provision "shell", inline: <<-SHELL
      mkdir -p /home/vagrant/.ssh
      echo "#{ENV_VARS['SSH_KEY_MAC']}" >> /root/.ssh/authorized_keys
      echo "#{ENV_VARS['SSH_KEY_HOME']}" >> /root/.ssh/authorized_keys
  
      parted /dev/sdb mklabel gpt
      parted /dev/sdb mkpart primary ext4 0% 60GB   # /var
      parted /dev/sdb mkpart primary ext4 60GB 80GB # /opt
      parted /dev/sdb mkpart primary ext4 80GB 100GB # /tmp
  
      mkfs.ext4 /dev/sdb1
      mkfs.ext4 /dev/sdb2
      mkfs.ext4 /dev/sdb3
  
      mkdir -p /mnt/var /mnt/opt /mnt/tmp
      mount /dev/sdb1 /mnt/var
      mount /dev/sdb2 /mnt/opt
      mount /dev/sdb3 /mnt/tmp
  
      echo '/dev/sdb1 /var ext4 defaults 0 2' >> /etc/fstab
      echo '/dev/sdb2 /opt ext4 defaults 0 2' >> /etc/fstab
      echo '/dev/sdb3 /tmp ext4 defaults 0 2' >> /etc/fstab
  SHELL
  end
end
