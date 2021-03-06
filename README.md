# vagrant-boxes

This package aims to provide a standardized way of building preconfigured boxes which
can be packaged to be used in projects.

The entire config is for a standard Symfony/PHP development environment including MySQL,
MongoDB, NGINX and other needed tools. Since the configuration is documented within the
ansible scripts it is easy to setup similar production nodes. 

The official supported boxes are provided through https://atlas.hashicorp.com. At the 
moment `peterrehm/xenial-php7` (based on `bento/ubuntu-16.04`) and `peterrehm/trusty-php7` 
(based on `ubuntu/trusty64`) are the supported versions.

Meanwhile there will be `peterrehm/trusty-php56` which won't receive updates any more.
The relevant configuration for this box can be found in the php5 branch.

If you want to build your own boxes it is as easy as

````sh
$ cd ~
$ git clone https://github.com/peterrehm/vagrant-boxes.git
$ cd vagrant-boxes
$ vagrant up
$ vagrant package --base xenial-php7.vb
````

You can then upload `~/vagrant-boxes/package.box` and upload e.g. to Atlas.

````sh
# add a new version of the box
curl https://atlas.hashicorp.com/api/v1/box/peterrehm/xenial-php7/versions \
        -X POST \
        -d version[version]='0.3.0' \
        -d access_token={token}

# add a new provider
curl https://atlas.hashicorp.com/api/v1/box/peterrehm/xenial-php7/version/0.3.0/providers \
-X POST \
-d provider[name]='virtualbox' \
-d access_token={token}

# 2-step upload process
curl https://atlas.hashicorp.com/api/v1/box/peterrehm/xenial-php7/version/0.3.0/provider/virtualbox/upload?access_token={token}
curl -X PUT --upload-file package.box https://binstore.hashicorp.com/{response token of previous command}

# release the version
curl https://atlas.hashicorp.com/api/v1/box/peterrehm/xenial-php7/version/0.3.0/release \
        -X PUT \
        -d access_token={token}
````
