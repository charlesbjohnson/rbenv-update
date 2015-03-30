Vagrant.configure(2) do |config|
  config.vm.box = 'chef/ubuntu-14.04'

  config.vm.synced_folder '~/.ssh', '/home/vagrant/.ssh', type: 'rsync',
    rsync__args: %w[--verbose --archive --compress --copy-links]

  config.vm.provision :shell, inline: <<-SCRIPT
    apt-get -qq update
    apt-get -qq install git-core zsh software-properties-common python-software-properties
    add-apt-repository --yes ppa:fish-shell/nightly-master
    apt-get -qq update
    apt-get -qq install fish

    chown --recursive vagrant:vagrant /home/vagrant/.rbenv
  SCRIPT

  config.vm.provision :shell, privileged: false, inline: <<-SCRIPT
    if [[ ! -d ~/tmp ]]; then
      git clone --quiet git@github.com:sstephenson/rbenv.git ~/tmp/rbenv
      cp --recursive ~/tmp/rbenv/* ~/.rbenv

      echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
      echo 'eval "$(rbenv init -)"' >> ~/.bash_profile

      cp ~/.bash_profile ~/.zshrc

      mkdir --parents ~/.config/fish
      echo 'set -x PATH $HOME/.rbenv/bin $PATH' > ~/.config/fish/config.fish
      echo 'source (rbenv init - | psub)' >> ~/.config/fish/config.fish
    fi
  SCRIPT

  # Using rsync so that any file changes are uni-directional (host ---> guest)
  # use `vagrant rsync-auto` to auto update when making changes
  config.vm.synced_folder '.', '/home/vagrant/.rbenv/plugins/rbenv-update', type: 'rsync'
end
