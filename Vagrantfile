Vagrant.configure("2") do |config|
  # https://docs.vagrantup.com

  # https://app.vagrantup.com/felipecassiors/boxes/ubuntu1804-4dev
  config.vm.box = "felipecassiors/ubuntu1804-4dev"

  # Expose VM port to host, enable public access
  # config.vm.network "forwarded_port", guest: 8080, host: 8080

  # Expose VM port to host, disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Private network mode
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Bridge network mode
  # config.vm.network "public_network"

  # Share folder
  # config.vm.synced_folder "C:/", "/c/"
  config.vm.synced_folder "~/Repos", "/home/vagrant/Repos"

  config.vm.provider "virtualbox" do |vb|
    # Set the display name of the VM in VirtualBox
    vb.name = "our-vm"
    # Customize the amount of memory on the VM:
    # vb.memory = "4096"
    # Customize the amount of CPU on the VM:
    # vb.cpus = "2"
  end

  # Run a shell script in first run
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    set -euxo pipefail

    APT_GET='sudo DEBIAN_FRONTEND=noninteractive apt-get'

    # Upgrade system
    $APT_GET update
    $APT_GET dist-upgrade -y
    $APT_GET autoremove -y
    sudo snap refresh

    # Set keyboard layout to Portuguese (Brazil)
    gsettings set org.gnome.desktop.input-sources sources "[('xkb', 'br')]"

    # Set timezone to America/Sao_Paulo
    sudo rm /etc/localtime && sudo ln -s /usr/share/zoneinfo/America/Sao_Paulo  /etc/localtime

    # Install VS Code extensions for Flutter and Prettier
    code --install-extension Dart-Code.flutter --install-extension esbenp.prettier-vscode

    sudo snap install android-studio --classic

    mkdir -p "$HOME/Android/Sdk"
    pushd "$HOME/Android/Sdk"
    command_line_tools='commandlinetools.zip'
    wget -q https://dl.google.com/android/repository/commandlinetools-linux-6200805_latest.zip -O $command_line_tools
    unzip -q $command_line_tools
    rm -f $command_line_tools
    export PATH="$(realpath tools/bin/):$PATH"
    echo "export PATH=\"$(realpath tools/bin/):\$PATH\"" >> "$HOME/.bashrc"
    popd

    mkdir "$HOME/.android"
    touch "$HOME/.android/repositories.cfg"
    yes | sdkmanager "platform-tools" "platforms;android-29" "build-tools;29.0.3" "sources;android-29" "patcher;v4"

    mkdir "$HOME/development"
    pushd "$HOME/development"
    git clone https://github.com/flutter/flutter.git -b stable
    export PATH="$(realpath flutter/bin/):$PATH"
    echo "export PATH=\"$(realpath flutter/bin/):\$PATH\"" >> "$HOME/.bashrc"
    popd

    flutter channel beta
    flutter upgrade
    flutter config --enable-web
    flutter precache

  SHELL

end
