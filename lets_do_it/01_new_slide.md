


!SLIDE 
# instala o lettuce #

    @@@ sh
    $ sudo pip install lettuce


!SLIDE 
# cria a app de pessoas #

    @@@ sh
    $ ./manage.py startapp pessoas


!SLIDE 
# instala as apps #


    @@@ sh
    INSTALLED_APPS = (
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
        'django.contrib.messages',
        'django.contrib.admin',
        'lettuce.django', # <==
        'inventario.pessoas',# <==
    )



!SLIDE 
# cria as features de pessoas #

    @@@ sh
    $ mkdir pessoas/features






!SLIDE 

    @@@ sh
    $ tree 
    ├── __init__.py
    ├── inventario.db
    ├── manage.py
    ├── pessoas
    │   ├── features # <==
    │   ├── __init__.py
    │   ├── models.py
    │   ├── tests.py
    │   └── views.py
    ├── settings.py
    ├── templates
    └── urls.py


!SLIDE 
# cria o arquivo de features#

    @@@ sh
    cd pessoas/features
    vi pessoas.feature



!SLIDE 

    @@@ spec 
    # language: pt-br
    Funcionalidade: criacao de pessoas 
      Como o administrador
      Eu crio as pessoas pelo admin
      Para verificar que esta tudo configurado

      Cenário: Verificar que os modelos existem e estao no admin
        Dado que a aplicacao pessoas tem o modelo Pessoa
        Entao a aplicacao tem admin
        E o modelo esta registrado



!SLIDE 
# harvest #

    @@@ sh
    $ ./manage.py harvest



!SLIDE 

    @@@ sh
    Djangos builtin server is running at 0.0.0.0:8000

    Funcionalidade: criacao de pessoas                           # pessoas/features/pessoas.feature:3
      Como o administrador                                       # pessoas/features/pessoas.feature:4
      Eu crio as pessoas pelo admin                              # pessoas/features/pessoas.feature:5
      Para verificar que esta tudo configurado                   # pessoas/features/pessoas.feature:6

      Cenário: Verificar que os modelos existem e estao no admin # pessoas/features/pessoas.feature:8
        Dado que a aplicacao pessoas tem o modelo Pessoa         # pessoas/features/pessoas.feature:9
        Entao a aplicacao tem admin                              # pessoas/features/pessoas.feature:10
        E o modelo esta registrado                               # pessoas/features/pessoas.feature:11

!SLIDE 

    @@@ sh
    1 feature (0 passed)
    1 scenario (0 passed)
    3 steps (3 undefined, 0 passed) # <==

!SLIDE 

    @@@ sh
    You can implement step definitions for undefined steps with these snippets:
    
    # -*- coding: utf-8 -*-
    from lettuce import step

    @step(u'Dado que a aplicacao pessoas tem o modelo Pessoa')
    def dado_que_a_aplicacao_pessoas_tem_o_modelo_pessoa(step):
        pass
    @step(u'Entao a aplicacao tem admin')
    def entao_a_aplicacao_tem_admin(step):
        pass
    @step(u'E o modelo esta registrado')
    def e_o_modelo_esta_registrado(step):
        pass

!SLIDE 


    @@@ Python
    # -*- coding: utf-8 -*-
    from lettuce import step
    from lettuce import world


    @step(u'Dado que a aplicacao +([^ ]*) tem o modelo +([^ ]*)')
    def dado_que_tenho_a_aplicacao_e_modelo(step, aplicacao, modelo):
      world.aplicacao = aplicacao
      world.modelo = modelo
      try:
        foo = __import__("inventario.%s.models"% aplicacao, globals(), locals(), [modelo,], -1)
        real = getattr(foo, modelo)
        world.this_model = real 
      except ImportError:
        assert False, "nao encontrei o %s.%s"%(aplicacao,modelo)


!SLIDE 
# harvest #

    @@@ sh
    $ ./manage.py harvest


!SLIDE 

    @@@ sh

    1 feature (0 passed)
    1 scenario (0 passed)
    3 steps (1 failed, 2 undefined, 0 passed)

    You can implement step definitions for undefined steps with these snippets:

    ...




!SLIDE 
# Cria os modelos #

    @@@ python
    class Pessoa(models.Model):
      nome = models.CharField(max_length=64)
      cargo = models.CharField(max_length=32)


!SLIDE 
# harvest #

    @@@ sh
    $ ./manage.py syncdb
    $ ./manage.py harvest



!SLIDE 

    @@@ sh
    1 feature (0 passed)
    1 scenario (0 passed)
    3 steps (2 undefined, 1 passed)

    You can implement step definitions for undefined steps with these snippets:

    ...



!SLIDE 

    @@@ python
    @step(u'Entao a aplicacao tem admin')
    def entao_a_aplicacao_tem_admin(step):
      try:
        foo = __import__("inventario.%s"% world.aplicacao, globals(), locals(), ["admin",], -1)
        admin = getattr(foo, "admin")
        world.admin = admin 
      except ImportError:
        assert False, "nao encontrei o admin"
    

!SLIDE 
# harvest #

    @@@ sh
    $ ./manage.py harvest



!SLIDE 

    @@@ sh
    AssertionError: nao encontrei o admin

    1 feature (0 passed)
    1 scenario (0 passed)
    3 steps (1 failed, 1 undefined, 1 passed)

!SLIDE 
# cria o arquivo mesmo que vazio #

    @@@ sh
    $ vi pessoas/admin.py
    :wq

!SLIDE 
# harvest #

    @@@ sh
    $ ./manage.py harvest



!SLIDE 

    @@@ sh
    1 feature (0 passed)
    1 scenario (0 passed)
    3 steps (1 undefined, 2 passed)

    You can implement step definitions for undefined steps with these snippets:
    ...


!SLIDE 

    @@@ python
    # -*- coding: utf-8 -*-
    from lettuce import step

    @step(u'E o modelo esta registrado')
    def e_o_modelo_esta_registrado(step):
      try:
        assert world.this_model in world.admin.admin.site._registry.keys(), "modelo nao encontrado"
      except AttributeError:
        assert False, "nao encontrei o registro no admin"


!SLIDE 

    @@@ sh
    AssertionError: nao encontrei o registro no admin

    1 feature (0 passed)
    1 scenario (0 passed)
    3 steps (1 failed, 2 passed)


!SLIDE 

    @@@ python
    #coding:utf8

    from django.contrib import admin

    from inventario.pessoas.models import Pessoa

    admin.site.register(Pessoa)

!SLIDE 

    @@@ sh
    1 feature (1 passed)
    1 scenario (1 passed)
    3 steps (3 passed)


!SLIDE 
# economia de codigo#

    @@@ sh
    $ ./manage.py startapp hardware
    
    # inclui o hardware nas INSTALLED_APPS 
    
    $ cd hardware/
    $ mkdir features
    $ vi features/partesbasicas.feature




!SLIDE 

    @@@ sh
    # -*- coding: utf-8 -*-
    # language: pt-br
    Funcionalidade: Verificar que existam todos os modelos necessarios
      Como o administrador
      Eu crio computadores e componentes pelo admin
      Para verificar se tudo está la

      Cenário: Verificar que tenho computadores
        Dado que a aplicacao hardware tem o modelo Computador
        Entao a aplicacao tem admin
        E o modelo esta registrado
     
      Cenário: Verificar que tenho os componentes
        Dado que a aplicacao hardware tem o modelo Componente
        Entao a aplicacao tem admin
        E o modelo esta registrado

