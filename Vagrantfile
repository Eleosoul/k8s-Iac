require 'fileutils'

# Функция для загрузки переменных из .env
def load_env(file)
  return {} unless File.exist?(file)
  env = {}
  File.readlines(file).each do |line|
    key, value = line.strip.split('=', 2)
    next if key.nil? || value.nil?
    env[key] = value.gsub(/\"/, '')  # Убираем кавычки
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
    end

    master.vm.provision "shell", inline: <<-SHELL
      mkdir -p /home/vagrant/.ssh
      echo "#{ENV_VARS['SSH_KEY_MAC']}" >> /root/.ssh/authorized_keys
      echo "#{ENV_VARS['SSH_KEY_HOME']}" >> /root/.ssh/authorized_keys
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
    end

    worker.vm.provision "shell", inline: <<-SHELL
      mkdir -p /home/vagrant/.ssh
      echo "#{ENV_VARS['SSH_KEY_MAC']}" >> /root/.ssh/authorized_keys
      echo "#{ENV_VARS['SSH_KEY_HOME']}" >> /root/.ssh/authorized_keys
    SHELL
  end
end







# Vagrant.configure("2") do |config|
#   # Настройки мастер-ноды
#   config.vm.define "master" do |master|
#     master.vm.box = "almalinux/9"
#     master.vm.hostname = "master"
#     master.vm.network "public_network", bridge: "Realtek PCIe GbE Family Controller", ip: "192.168.8.3"

#     master.vm.provider "virtualbox" do |vb|
#       vb.memory = "4096"
#       vb.cpus = 2
#     end

#     # Добавляем публичный SSH-ключ
#     master.vm.provision "shell", inline: <<-SHELL
#       mkdir -p /home/vagrant/.ssh
#       echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFPQz2LUxDYVGK9H1FdU5D+uYYT4BQdWq3mpca1/R9kl alex@MacBook-Air-alex.local" >> /home/vagrant/.ssh/authorized_keys
#       chmod 600 /home/vagrant/.ssh/authorized_keys
#       chown -R vagrant:vagrant /home/vagrant/.ssh
#     SHELL
#   end

#   # Настройки воркер-ноды
#   config.vm.define "worker" do |worker|
#     worker.vm.box = "almalinux/9"
#     worker.vm.hostname = "worker"
#     worker.vm.network "public_network", bridge: "Realtek PCIe GbE Family Controller", ip: "192.168.8.4"

#     worker.vm.provider "virtualbox" do |vb|
#       vb.memory = "16384"
#       vb.cpus = 8
#     end

#     # Добавляем публичный SSH-ключ
#     worker.vm.provision "shell", inline: <<-SHELL
#       mkdir -p /home/vagrant/.ssh
#       echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFPQz2LUxDYVGK9H1FdU5D+uYYT4BQdWq3mpca1/R9kl alex@MacBook-Air-alex.local" >> /home/vagrant/.ssh/authorized_keys
#       chmod 600 /home/vagrant/.ssh/authorized_keys
#       chown -R vagrant:vagrant /home/vagrant/.ssh
#     SHELL
#   end
# end
