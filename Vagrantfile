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







