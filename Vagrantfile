VAGRANT_PLUGINS = %w(vagrant-hostsupdater)

VAGRANT_PLUGINS.each do |plugin|
  unless Vagrant.has_plugin?(plugin)
    puts "Plugin '#{plugin}' is not installed! Installing..."
    system "vagrant plugin install #{plugin}"
  end
end

# Load .env file
DOTENV_FILE = '.env'
begin
  File.read(DOTENV_FILE).each_line do |line|
    line.strip!
    next if line.empty? || line.start_with?('#')
    k, v = line.split('=').map { |s| s.gsub('\'', '').strip }
    ENV[k] = v
  end
rescue Exception => e
  fail "File #{DOTENV_FILE} is invalid."
end if File.exists?(DOTENV_FILE)

# Configure all the things \o/
HOST_SSH_KEYS = begin
  glob_pattern = ENV['HOST_SSH_KEYS'] || '*'
  Dir.glob("#{File.expand_path('~')}/.ssh/{#{glob_pattern}}.pub")
end

USER_NAME = ENV['USER_NAME'] || 'vagrant'
BOX_NAME = ENV['BOX_NAME'] || 'base'
HOSTNAME = ENV['HOSTNAME'] || 'vagrant'
PRIVATE_IP = ENV['PRIVATE_IP'] || '192.168.150.150'

USER_HOME = "/home/#{USER_NAME}"
AUTHORIZED_KEYS = HOST_SSH_KEYS.map do |p|
  puts "==>  custom: Using public-key '#{p}'"
  File.read(p).strip
end

Vagrant.configure(2) do |config|
  config.vm.tap do |vm|
    vm.box = BOX_NAME
    vm.hostname = HOSTNAME

    vm.network('private_network', ip: PRIVATE_IP)

    vm.provision 'shell', inline: <<-EOF
      id -u #{USER_NAME} > /dev/null 2>&1 || useradd #{USER_NAME}
      usermod --home #{USER_HOME} #{USER_NAME} > /dev/null 2>&1
      echo "#{USER_NAME} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/00-vagrant-sudo
      mkdir -p #{USER_HOME}/.ssh
      echo "#{AUTHORIZED_KEYS.join("\n")}" > #{USER_HOME}/.ssh/authorized_keys
    EOF

    vm.synced_folder '.', "#{USER_HOME}/app", type: 'nfs'
  end

  config.hostsupdater.aliases = ENV['HOSTNAME_ALIASES'].split(',').map(&:strip) if ENV['HOSTNAME_ALIASES']
end
