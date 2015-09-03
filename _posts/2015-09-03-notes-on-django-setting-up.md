---
layout: post
title: "Notes on Django: Setting Up"
date: "2015-09-03"
tags: python, django, vagrant
---

I'm starting to develop multiple Django projects, but am still referring to the *(excellent)* [Django tutorial][djt] to get set up, so I thought I'd write out the steps I take to set up a new Django project and it's associated development environment, just to help remember it and have an easy reference for the future.

<!--more-->

I work on my Django projects within a [Vagrant][v] environment.  This creates a nice, easy-to-use, isolated environment that is simple to bring up, tear down and replicate.  It also means I, controversially, don't use [virtualenv][ve], as Vagrant isolates things for me.  Though I admit, I'm still an amateur and am probably overlooking something important, especially as nothing I've made has yet made it into production.  To be honest I'd really like to move everything to [Docker][dkr] but I haven't yet had the time to properly learn Docker or explore the feasability of this.  One of the nice things about Vagrant is that I have a separate VM for my PostgreSQL database.  So, because I'm still learning Postgres, and run into migration problems when changing models, it is easy to just destroy and rebuild my database backend quickly.  This does however destroy any data I have, which currently isn't a big problem.  I want to put aside some time in my workflow for creating a script that populates the database with some basic data for development.  But I really need to spend some time learning how to properly deal with the database and migrations, of course.

My Vagrant setup can be found on my [Github][gv], and consists of a Vagrantfile, a couple of bash scripts to provision each machine, and a shared folder that contains a pip requirements file, and will go on to contain my Django project's root folder.  This means I can work on my Django project files on my laptop rather than in the VM.  I have a private [GitLab][gitl] server that I use to house all my project repositories, with my root git folder containing the Vagrant setup and this shared folder.

This is my Vagrantfile:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

# These are the ip & port variables that can be changed on a per-project basis
vagrant_root = File.dirname(__FILE__)
django_8000_fp = "8011"
django_80_fp = "8091"
db_8000_fp = "8012"
db_80_fp = "8092"
db_postgres_fp = "25432"
django_private_ip = "10.101.9.101"
db_private_ip = "10.101.9.102"

Vagrant.configure(2) do |config|

    config.vm.box = "ubuntu/trusty64"
    config.vm.box_url = "https://atlas.hashicorp.com/ubuntu/boxes/trusty64"
    config.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
        vb.cpus = "1"
        vb.gui = false  # I sometimes run into problems getting the environment
                        # set up that are fixed by 'turning on' the Virtualbox
                        # screen for the VM, by changing this to true
    end

    if Vagrant.has_plugin?("vagrant-hostmanager")
        # this is a great Vagrant plugin that takes care of the VM /etc/hosts
        # files
        config.hostmanager.enabled = true
        config.hostmanager.manage_host = true
        config.hostmanager.ignore_private_ip = false
        config.hostmanager.include_offline = true
    end

    # The following sets up two machines, one called django, the other called db.
    # They both currently have both a NAT network and a private network, which
    # I think is unneccessary, I initially did it so the dev machine would have
    # an ip address from the staging server subnet to make setting it up there
    # easier.  This has created a problem for me in the past when pushing changes
    # to my Gitlab repos, as the Vagrant machine set up a route to the /16
    # subnet which included my Gitlab server, so I would have to shut down my
    # Vagrant machines and delete the route from my routing table to be able to
    # push commits to my remote repo. Ugh.
    config.vm.define "django" do |django|
        django.vm.hostname = "django"
        django.vm.network "private_network", ip: django_private_ip, :netmask => "255.255.0.0"
        django.vm.network "forwarded_port", guest: 8000, host: django_8000_fp
        django.vm.network "forwarded_port", guest: 80, host: django_80_fp
        django.vm.synced_folder "#{vagrant_root}/shared", "/home/vagrant/shared"
        # set up the shared folder in the shared directory within the current
        # directory
        django.vm.provision :shell, :path => "#{vagrant_root}/provision/djangonode-setup.sh"
        # initiate the provisioning bash script.
    end

    config.vm.define "db" do |db|
        db.vm.hostname = "db"
        db.vm.network "private_network", ip: db_private_ip, :netmask => "255.255.0.0"
        db.vm.network "forwarded_port", guest: 8000, host: db_8000_fp
        db.vm.network "forwarded_port", guest: 80, host: db_80_fp
        db.vm.network "forwarded_port", guest: 5432, host: db_postgres_fp
        db.vm.provision :shell, :path => "#{vagrant_root}/provision/dbnode-setup.sh"
    end

end
```

The django provision file just installs pip and runs the requirements file.  The db provision file installs Postgres and sets up the database.

So, once I've updated any variables in this Vagrantfile and the db provision file, I'll set up the new remote repo on my Gitlab server.  Then we unchain Django and get developing...

```bash
laptop$ cd root-project-dir
laptop$ vagrant up
Bringing machine 'django' up with 'virtualbox' provider...
Bringing machine 'db' up with 'virtualbox' provider...
[...omitted for brevity]
laptop$
laptop$ vagrant ssh django
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.13.0-55-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

 System information disabled due to load higher than 1.0

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud


vagrant@django:~$ cd shared/
vagrant@django:~/shared$ django-admin startproject mysite
```

At this point I need to change the Django settings file to reflect the Postgres database setup:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'db',
        'USER': 'django',
        'PASSWORD': 'django',
        'HOST': 'db',
        'PORT': '5432',
    }
}   # Obviously these aren't the actual names and passwords I use, duh.  Though
    # the vagrant hostmanager plugin does mean you just have to put db, the name
    # of the Postgres VM, in as the Database host name, ahhhh niiice.
```

Whilst in the settings file let's update our location:

```python
LANGUAGE_CODE = 'en-gb'

TIME_ZONE = 'Europe/London'
```

and create the `ststic` directory for CSS, JavaScript, fonts etc. and `media` directory for images, pdf's and the like:

```python
STATIC_URL = '/static/'
STATIC_ROOT = '/home/vagrant/shared/mysite/appname/static/'
MEDIA_URL = '/static/media/'
MEDIA_ROOT = '/home/vagrant/shared/mysite/appname/static/media/'
```

Don't forget to actually create these directories!
Back in the django VM:

```bash
vagrant@django:~/shared$ cd mysite
vagrant@django:~/shared/mysite$ python manage.py migrate
[...omitted for brevity]
vagrant@django:~/shared/mysite$
vagrant@django:~/shared/mysite$ python manage.py runserver 0.0.0.0:8000

Performing system checks...

System check identified no issues (0 silenced).
September 03, 2015 - 12:03:15
Django version 1.8.2, using settings 'mysite.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

Running the Django server at `0.0.0.0:8000` makes the site accessible from outside the VM, on the port specified in the Vagrantfile.  So, if I used the Vagrantfile above, which contains `django_8000_fp = "8011"` then in the browser on my laptop I would got o `http://localhost:8011` which would bring up the Django site.  

If that works, go on to set up the app:

```bash
vagrant@django:~/shared/mysite$ python manage.py startapp appname
# pop your app in the INSTALLED_APPS section of the settings.py file
vagrant@django:~/shared/mysite$ python manage.py makemigrations appname
vagrant@django:~/shared/mysite$ python manage.py migrate
# might as well create the admin superuser while we're here
vagrant@django:~/shared/mysite$ python manage.py createsuperuser
```

Go build stuff.

[djt]: https://docs.djangoproject.com/en/1.8/intro/tutorial01/
[v]:   https://www.vagrantup.com/
[ve]:  https://virtualenv.pypa.io/en/latest/
[dkr]: https://www.docker.com/
[gv]:  https://github.com/bordeltabernacle/Vagrant-Django-PostgreSQL
[gitl]: https://about.gitlab.com/downloads/
