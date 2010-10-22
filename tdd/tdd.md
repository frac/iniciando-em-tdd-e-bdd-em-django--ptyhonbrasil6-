!SLIDE bullets incremental
# Test Driven Development #

* é uma das estrategias
* pode ser usada de forma subversiva
* fácil
* mas é só uma das estrategias

!SLIDE bullets incremental
# TDD #


* Só escreve **código** quando testes falham

* Só escreve **testes** quando testes passam

!SLIDE
# Mãos a obra #



!SLIDE
## instala o django ##

    @@@ Sh
    $ sudo easy_install pip
    $ sudo pip install django

!SLIDE
## cria o projeto ##

    @@@ sh
    $ django-admin.py startproject inventario

!SLIDE

    @@@ sh
    $ tree inventario/

    inventario/
    ├── __init__.py
    ├── manage.py
    ├── settings.py
    └── urls.py

!SLIDE

    @@@ sh
    $ cd inventario/

    $ vi settings.py

!SLIDE
## hackeia as settings ##

    @@@ python
    import os

    PROJECT_PATH = os.path.abspath( os.path.split(__file__)[0] )

    MEDIA_ROOT = os.path.join( PROJECT_PATH, "media")

    TEMPLATE_DIRS = (
        os.path.join( PROJECT_PATH, "templates"),
    )


!SLIDE
## configura o banco ##

    @@@ python

    DATABASES = {
      'default': {
        'ENGINE':
          'django.db.backends.sqlite3',
        'NAME':
          PROJECT_PATH + "/inventario.db",
      }
    }


!SLIDE
# quase pronto #

    @@@ sh
    $ chmod +x manage.py

    $ ./manage.py syncdb

!SLIDE
# testa #

    @@@ sh
    ./manage.py test

    ------------------------------------
    Ran 0 tests in 0.000s

    OK
    Destroying test database 'default'...


!SLIDE bullets
# TDD #


* Só escreve **código** quando testes falham

* Só escreve **testes** quando testes passam



!SLIDE bullets incremental
# Passou #

* Escreve testes


!SLIDE
# Mais Testes, então #

    @@@sh
    $ ./manage.py startapp hardware
    $ cd hardware/

!SLIDE
# disclaimer #

    @@@sh
    $ rm tests.py
    $ mkdir tests
    $ touch tests/__init__.py
    $ touch tests/test_models.py


!SLIDE
# vi tests/test_models.py #

    @@@ python
    #coding:utf8
    from django.test import TestCase

    class ModelTest(TestCase):


!SLIDE
# Teste de importação #

    @@@ python

    def test_existe(self):
      """ O computador esta la? """
      try:
        from hardware.models import Computador
      except ImportError:
        self.fail('Nao existe o model computador')


!SLIDE
# Inclui a app no projeto #

    @@@ python

    INSTALLED_APPS = (
        ...
        'inventario.hardware',
    )




!SLIDE
# Testa #

    @@@ python


    ./manage.py test

    ------------------------------------
    Ran 0 tests in 0.000s

    OK
    Destroying test database 'default'...


!SLIDE bullets incremental
# 0 testes! #

* voce tem que declarar os testes explicitamente no __init__.py
* ou tem?

!SLIDE bullets
# nose #

* Acha testes para você sem que você tenha que por eles no __init__.py

* Dá pra chamar o pdb no ponto em que falha ( --pdb-failures) (ou ipdb)


!SLIDE
# django-nose #

    @@@ sh

    $ pip install nose
    $ pip install django-nose
    $ pip install NoseNotify #opcional


!SLIDE
# settings.py #


    @@@ python

    TEST_RUNNER = 'django_nose.NoseTestSuiteRunner'

    INSTALLED_APPS = (
        ...
        'south', # migracoes
        'django_nose', # depois do south
    )
    NOSE_ARGS = [ '--with-notify',
        #'--pdb-failures',
    ]





!SLIDE
# Testa de novo #

    @@@ python

    F
    ====================================
    FAIL: O computador esta la?
    ------------------------------------
    Traceback (most recent call last):
      File "test_models", line 18, in test_existe
        self.fail('Nao existe o model computador')
    AssertionError: Nao existe o model computador
    ------------------------------------
    Ran 1 test in 0.003s


!SLIDE bullets
# TDD #


* Só escreve **código** quando testes falham

* Só escreve **testes** quando testes passam


!SLIDE bullets incremental
# Falhou #

* Escreve código

!SLIDE
# vi hardware/models.py #

    @@@ python

    class Computador(models.Model):
        """representa um computador"""
        pass

!SLIDE
# testa #

    @@@ python

    .
    ------------------------------------
    Ran 1 test in 0.014s



!SLIDE bullets incremental
# O que voce esta testando? #

* tenha certeza do que voce esta testando
* nao teste a framework


!SLIDE 
# propriedades #

    @@@ python
    from django.test import TestCase
    from inventario.hardware.models import Computador

    class TestHardwareProps(TestCase):
      """Verifica que as propriedades do app hardware"""

      def testa_computador_props(self):
        computador = Computador()
        self.assertTrue(hasattr(computador, "desc"))


!SLIDE 
# propriedades #

    @@@ sh
    F..
    ============================
    FAIL: testa_computador_props
    ----------------------------
    Traceback (most recent call last):
      File "inventario/hardware/tests/base_test.py", 
        line 9, in testa_computador_props
        self.assertTrue(hasattr(computador, "desc"))
    AssertionError

    ----------------------------
    Ran 3 tests in 0.003s



!SLIDE 
# escreve codigo #

    @@@ python
    class Computador(models.Model):
        desc = models.CharField(max_length=64)


!SLIDE 
# faz sentido? #

    @@@ python
    def testa_computador_props(self):
        computador = Computador()
        self.assertTrue(hasattr(computador, "desc"))
        desc = "a"*64
        computador.desc = desc
        try:
            computador.save()
        except DatabaseError:
            self.fail("Nao consegui salvar desc")

    
!SLIDE 
# E isso? #

    @@@ python
    ...        
    desc = "a"*65
    computador.desc = desc
    try:
        computador.save()
        self.fail("tamanho maximo nao respeitado")
    except DatabaseError:
        pass
        

!SLIDE bullets 
# TDD #

* nao é a unica alternativa


