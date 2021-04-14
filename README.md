## Notes about installing on Ubuntu:
Git Clone needs the SSH key installed on the server. (https://docs.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)
SQLite dev needs to be installed (sudo apt get install libsqlite3-dev)
Need Ruby 2.6.6 installed. Use rbenv and ruby-build

Basically: 
```
sudo apt install autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm5 libgdbm-dev rbenv nodejs gcc ruby-dev libxslt-dev libxml2-dev zlib1g-dev
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc
type rbenv
```
        
## install ruby-build
```
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
#rbenv install -l
rbenv install 2.6.6
rbenv global 2.6.6
ruby -v
```

Can't remember why, but install yarn:

```
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install yarn
```

```sh
git clone git@github.com:ohnyxjono/PasswordPusher.git
cd PasswordPusher
sudo gem install bundler
sudo bundle install --without development production test --deployment
bundle exec rake assets:precompile
RAILS_ENV=private bundle exec rake db:setup
foreman start internalweb
```

There are some issues with the listen gem. (not needed, done by default in this fork)
comment out config.file_watcher = ActiveSupport::EventedFileUpdateChecker from config/environments/development.rb 

This is discussed here: https://stackoverflow.com/questions/38663706/loaderror-could-not-load-the-listen-gem-rails-5



Run the app in foreman:
```
sudo apt install ruby-foreman
```
HTTPS:
Install Caddy (https://caddyserver.com/docs/install#debian-ubuntu-raspbian) and make a reverse proxy to pwpush. run caddy as a service
Caddy file example: 
`pwpush.domain.co.nz`

`reverse_proxy localhost:5000`

I found running pwpush in a tmux session worked the best/easiest. 
    make the session by typing tmux
    ctrl + b to get out of it and back to normal console
    tmux attach to get back into it
    
Restarting / recompiling pwpush after css or other changes (from the PasswordPusher dir):
```
bundle exec rake assets:precompile
RAILS_ENV=private bundle exec rake db:setup
foreman start internalweb
```

These notes might be incomplete. I'll update it if/when I rebuild the server or stand up another instance. 



![Password Pusher Front Page](https://s3-eu-west-1.amazonaws.com/pwpush/pwpush_logo_2014.png)

PasswordPusher is an opensource application to communicate passwords over the web. Links to passwords expire after a certain number of views and/or time has passed. 

Hosted at [pwpush.com](https://pwpush.com) but you can also easily run your own instance internally on Docker, Kubernetes, OpenShift or on Heroku with just a few steps.

[Follow PasswordPusher on Twitter](https://twitter.com/pwpush) for the latest news, updates and changes.

[![CircleCI](https://circleci.com/gh/pglombardo/PasswordPusher/tree/master.svg?style=svg)](https://circleci.com/gh/pglombardo/PasswordPusher/tree/master)

# 💾 Running your own Instance of PasswordPusher


## On Heroku

One click deploy to Heroku and get a fully configured running private instance of PasswordPusher immediately.

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/pglombardo/PasswordPusher)

## On Docker

Docker images of PasswordPusher are available on [Docker hub](https://hub.docker.com/u/pglombardo).

**➜ ephemeral**

    docker run -d -p "5000:5000" pglombardo/pwpush-ephemeral:latest

[Learn more](https://github.com/pglombardo/PasswordPusher/tree/master/containerization#pwpush-ephemeral)

**➜ using an External Postgres Database**

    docker run -d -p "5000:5000" pglombardo/pwpush-postgres:latest

[Learn more](https://github.com/pglombardo/PasswordPusher/tree/master/containerization#pwpush-postgres-external-database)

## With Docker Compose

Included in this repository is `containerization/pwpush-postgres/docker-compose.yaml` which can be used by simply running:

    docker-compose up -d
    docker-compose down

[Learn more](https://github.com/pglombardo/PasswordPusher/tree/master/containerization#pwpush-postgres)

## On Kubernetes

We currently don't supply a prebuilt Kubernetes YAML file yet but you can deploy the above Docker images using [this documentation](https://docs.docker.com/get-started/kube-deploy/).

## On Microsoft Azure

See [this blog post](https://tamethe.cloud/pwpush-host-your-own-using-azure-containers/) on how to deploy PasswordPusher to Azure by Craig McLaren.

## On OpenShift

See our [OpenShift documentation](https://github.com/pglombardo/PasswordPusher/tree/master/containerization#pwpush-openshift).

## From Source

Make sure you have git and Ruby installed and then:

```sh
git clone git@github.com:pglombardo/PasswordPusher.git
cd PasswordPusher
gem install bundler
bundle install --without development production test --deployment
bundle exec rake assets:precompile
RAILS_ENV=private bundle exec rake db:setup
foreman start internalweb
```

Then view the site @ [http://localhost:5000/](http://localhost:5000/).

_Note: You can change the listening port by modifying the
[Procfile](https://github.com/pglombardo/PasswordPusher/blob/master/Procfile#L2)_
