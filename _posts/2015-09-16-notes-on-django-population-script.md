---
layout: post
title: "Notes on Django: Population Script"
date: "2015-09-16"
tags: python, django, vagrant
---

After my last Django post I happened across [this population script][ps] from [Tango with Django][twd].  So I've updated it and adapted it to my own needs.  Ha, Screw You [Technical Debt!][td]

<!--more-->

`example models.py`

```python
class Genre(models.Model):
    name = models.CharField()


class Musician(models.Model):
    genre = models.ManyToManyField(Genre)  # Note the m2m field
    name = models.CharField()
    bio = models.TextField()
    alive = models.BooleanField()
```

'example populate.py'

```python
import os


def populate():

    print 'Populating Database...'
    print '----------------------\n'

    username = 'uname'
    email = 'uname@domain.com'
    password = 'secret'

    blues = add_genre('Blues')
    jazz = add_genre('Jazz')

    add_musician('Billie Holiday',
                'Eleanora Fagan, professionally known as Billie Holiday, was an American jazz musician and singer-songwriter. Nicknamed "Lady Day" by her friend and music partner Lester Young, Holiday had a seminal influence on jazz music and pop singing. Her vocal style, strongly inspired by jazz instrumentalists, pioneered a new way of manipulating phrasing and tempo.',
                False,
                blues, jazz)

    add_musician('Big Mama Thornton',
                "Willie Mae 'Big Mama' Thornton was an American rhythm and blues singer and songwriter. She was the first to record Leiber and Stoller's 'Hound Dog' in 1952, which became her biggest hit. It spent seven weeks at number one on the Billboard R&B charts in 1953 and sold almost two million copies. However, her success was overshadowed three years later, when Elvis Presley recorded his more popular rendition of 'Hound Dog'. Similarly, Thornton's 'Ball 'n' Chain' had a bigger impact when performed and recorded by Janis Joplin in the late 1960s."",
                False,
                blues)

    add_musician('Ella Fitzgerald',
                'Ella Jane Fitzgerald was an American jazz singer often referred to as the First Lady of Song, Queen of Jazz and Lady Ella. She was noted for her purity of tone, impeccable diction, phrasing and intonation, and a "horn-like" improvisational ability, particularly in her scat singing.',
                False,
                jazz)


    create_super_user(username, email, password)

    print '\nCurrently Populated:'
    print '--------------------\n'

    for m in Musician.objects.all():
        for g Genre.objects.filter(musician__name__startswith=m):
            print '- {0}: {1}'.format(str(m), str(g))

    print '\nSuperUser:', User.objects.get(is_superuser=True).username
    print '\n' + ('=' * 80) + '\n'


def add_genre(name):
    g, created = Genre.objects.get_or_create(name=name)
    '''
    get_or_create returns a tuple of (object, created), where
    the object is the db entry and created is a Boolean referring
    to whether the object was just created or not.  So, if nothing's
    gone wrong False will indicate the object already exists
    '''
    print '- Genre: {0}, Created: {1}'.format(str(g), str(created))
    return g


def add_musician(name, bio, alive, *genre):
    m, created = Musician.objects.get_or_create(name=name,
                                                bio=bio,
                                                alive=alive)
    m.genre.add(*genre)
    print '- Musician: {0}, Created: {1}'.format(str(m), str(created))
    return m


def create_super_user(username, email, password):
    '''
    for some reason get_or_create didn't work with creating the
    SuperUser so here is a try/except, with an IntegrityError
    raised if the SuperUser already exists
    '''
    try:
        u, created = User.objects.create_superuser(username, email, password)
        return u
    except IntegrityError:
        pass

if __name__ == '__main__':
    print '\n' + ('=' * 80) + '\n'
    import django
    os.environ.setdefault('DJANGO_SETTINGS_MODULE',
                          'Project.settings')
    django.setup()
    from app.models import Genre, Musician
    from django.contrib.auth.models import User
    from django.db import IntegrityError
    populate()  # Call the populate function, which calls the
                # add_genre and add_musician functions

```

And in our root project directory, where `manage.py` is, we run our script.


```bash
vagrant@django:~/shared/ProjectRootDirectory$ python populate.py
================================================================================

Populating Database...
----------------------

- Genre: Blues, Created: True
- Genre: Jazz, Created: True
- Musician: Billie Holiday, Created: True
- Musician: Big Mama Thornton, Created: True
- Musician: Ella Fitzgerald, Created: True

Currently Populated:
--------------------

- Billie Holiday: Jazz
- Billie Holiday: Blues
- Big Mama Thornton: Blues
- Ella Fitzgerald: Jazz

SuperUser: uname

================================================================================
```

So, buggering about with models and the database just got easier, now that it takes minutes to destroy it and rebuild it. Super.

[ps]: http://www.tangowithdjango.com/book/chapters/models.html#creating-a-population-script
[twd]: http://www.tangowithdjango.com/
[td]: http://www.discoposse.com/index.php/2014/11/09/pay-yourself-first-the-art-of-reducing-technical-debt/
