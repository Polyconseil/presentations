État des lieux du packaging python
==================================

----

Me, me & me
===========

- Xavier Fernandez

- Développeur chez Polyconseil depuis 3 ans

- Un des mainteneurs actuels de pip

----

Packaging
=========

- Domaine flou

- Processus entre le commit et le déploiement

- .tar.gz, git, .deb, .rpm, docker, etc

- Rendre le code disponible à l'interpréteur de son choix

- Packaging python souvent critiqué

----

Historique 1
============

- Février 1991: Python 0.9.0

- Janvier 1994: Python 1.0

- Septembre 2000: distutils (dans Python 1.6) => origine du setup.py

- Février 2003: PyPI (cheeseshop)


.. notes:
    permet de faire tar.gz et d'installer + helpers pour les extensions C
    Distutils allows you to structure your Python project so that it has a setup.py.
    Through this setup.py you can issue a variety of commands, such as creating a tarball out of your project, or installing your project.
    Distutils importantly also has infrastructure to help compiling C extensions for your Python package.
    Annonce PyPI: https://mail.python.org/pipermail/distutils-sig/2003-February/003172.html
    => un PoC qui part en prod


----

Historique 2
============

- Mars 2004: setuptools

- Mai 2005: easy_install

- 2006: buildout

- 2007: virtualenv

.. notes:
    setuptools: https://github.com/pypa/setuptools/commit/8423e1ed14ac1691c2863c6e8cac9230cf558d7b
    easy_install: https://github.com/pypa/setuptools/commit/d332c4c3c3eb7d65140f2094a36fdf9a1f2c254a

----

Période difficile ~ 2008-2012
=============================

- Septembre 2008: distribute

- Décembre 2008: Python 3

- 2008: pip

Situation:

- distutils / setuptools / distribute
- pip / easy_install
- Python 2/3
- PyPI peu stable


.. notes:
    Annonce distribute: https://mail.python.org/pipermail/distutils-sig/2008-September/010031.html
    Mentionner ce qu'apporte distutils, setuptools, pip, 

----

Du mieux
========

- Mai 2012: PEP 405 - Python Virtual Environments (python 3.3 avec venv)

- Février 2013: PEP 427 & wheel

- Mars-Juin 2013: remerge (unfork ?) distribute/setuptools

- Octobre 2013: PEP 453 - Explicit boostrapping of pip in Python installations ( dans 3.4)

- Septembre 2014: PEP 477: Backport ensurepip (PEP 453) to Python 2.7 (dans la 2.7.9)

- Fiabilisation de PyPI + réécriture moderne (Warehouse), déjà utilisable sur https://pypi.org

- Gros effort de spécifications


.. notes:
    Merge distribute/setuptools: https://mail.python.org/pipermail/distutils-sig/2013-March/020126.html

----

Nombreuses PEPs
===============

+ Février 2013: PEP 425 -- Compatibility Tags for Built Distributions

+ Février 2013: PEP 427 -- The Wheel Binary Package Format 1.0

+ Fin 2014: PEP 440 -- Version Identification and Dependency Specification

+ Septembre 2015: PEP 503 -- Simple Repository API

+ Novembre 2015: PEP 508 -- Dependency specification for Python Software Packages

+ Janvier 2016: PEP 513 -- A Platform Tag for Portable Linux Built Distributions

+ Mai 2016: PEP 518 -- Specifying Minimum Build System Requirements for Python Projects

----

PEP 425 & 427: Wheels (Février 2013)
====================================

- {distribution}-{version}(-{build tag})?-{python tag}-{abi tag}-{platform tag}.whl

- Format de packaging "tout prêt"

- Pas d'exécution de code / pas de compilation

- Installation plus rapide

- Besoin de ``setuptools`` & ``wheel`` pour les créer (``python setup.py bdist_wheel``)
  mais pas du tout pour les installer (depuis pip 1.4)

- Si ``setuptools`` & ``wheel`` sont dispos, pip crée un cache de wheels (depuis pip 7)

----

PEP 440 (Fin 2014) - Versions
=============================

- [N!]N(.N)*[{a|b|rc}N][.postN][.devN][+local_identifier]

- Se méfier des "-" (et des ``LegacyVersion``)

- ``pip install packaging``

.. code-block:: python

    >>> from packaging.version import parse
    >>> parse('3.4-dev')
    <Version('3.4.dev0')>
    >>> parse('3.4-dev-djangocong')
    <LegacyVersion('3.4-dev-djangocong')>
    >>> parse('3.4-dev+djangocong')
    <Version('3.4.dev0+djangocong')>
    >>> parse('3.4-dev-djangocong') < parse('1') < parse('3.4-dev+djangocong')
    True

----

PEP 440 (Fin 2014) - Specifiers
===============================

- Spécification des specifiers

.. code-block:: python

    >>> parse('3.6.0') in SpecifierSet('>=3.4,!=3.4.0,!=3.4.1')
    True

- Nouveaux opérateurs: ~= 1.10.6, != 1.3.4.*, ===djangocong17

- AND implicite

- ``'django-mptt==0.5.2,==0.6,==0.6.1'``

.. code-block:: python

    >>> parse('17-djangocong') in SpecifierSet('>=1.0,!=1.2')
    False
    >>> parse('17+djangocong') in SpecifierSet('>=1.0,!=1.2')
    True

- Depuis pip 6

----

PEP 503 (septembre 2015) & PyPI
===============================

- Spécification du contrat entre un installer (pip) et un index (PyPI)

- https://pypi.python.org/simple/foo/ => html contenant des <a href="lien_vers/fichier.whl">fichier.whl</a>

- Définition d'une normalisation de nom (déjà utilisée chez setuptools)

.. code-block:: python

    re.sub(r"[-_.]+", "-", name).lower()

    from packaging.utils import canonicalize_name

- ``canonicalize_name('Hello.-.World')`` => ``'hello-world'``

- Attribut data-requires-python ajouté en juillet 2016 (défini PEP-345, Avril 2005)

- Hors de la PEP: Impossibilité de réuploader un numéro de version déjà uploadé

----

PEP 345 - Python-Requires
=========================

- ``python_requires='>=3.4.2',`` dans le setup.py

Erreur à l'installation:

``your_pkg requires Python '>=3.4.2' but the running Python is 2.7.13``

(ou si l'index supporte le ``data-requires-python``, il n'essaye même pas)

- Pour ``pip``: ``python_requires='>=2.7,!=3.0.*,!=3.1.*,!=3.2.*',``

- Depuis pip 9


----

PEP 508 (novembre 2015) - Dépendances
=====================================

Gros travail de spécification du comportement actuel de pip/setuptools avec les extras, les liens directs, les marqueurs d'environnement etc

- ``requests[security,tests] >= 2.8.1, == 2.8.* ; python_version < "2.7.10"``

- ``pip @ https://github.com/pypa/pip/archive/1.3.1.zip#sha1=da9234ee99...0a148ea686``

.. code-block:: python

    >>> from packaging.requirements import Requirement
    >>> req = Requirement(
    ... 'requests[security,tests] ~= 2.8.1 ; python_version < "2.7.10"'
    ... )
    >>> req
    <Requirement('requests[security,tests]~=2.8.1; python_version < "2.7.10"')>
    >>> req.name
    'requests'
    >>> req.marker
    <Marker('python_version < "2.7.10"')>

----


Exemple inspiré de IPython
==========================

.. code-block:: python

    from setuptools import setup

    setup(
        name='fakepython',
        extras_require={
            ':python_version == "3.3"': ['pathlib2'],
            ':sys_platform == "win32" and python_version < "3.6"': [
                'win_unicode_console>=0.5'
            ],
            # ...
            'doc': ['Sphinx>=1.3'],
            'test:python_version >= "3.4"': ['numpy'],
        },
        install_requires=[
            'pep8; python_version == "2.6"',
        ],
    )

----

PEP 513 - manylinux1 (janvier 2016)
===================================

- Wheel binaires pour linux et autorisées sur PyPI

- Basés sur CentOS 5.11 et une liste de vieilles versions de librairies supposées fournies

- Librairies externes linkées statiquement ou intégrées à la wheel

- Outils pour construire/auditer les wheels: https://github.com/pypa/manylinux

- un peu piégeux car ce n'est pas toujours ce que l'on veut

.. code-block:: python

    try:
        import _manylinux
        return bool(_manylinux.manylinux1_compatible)
    except (ImportError, AttributeError):
        pass
    return have_compatible_glibc(2, 5)

- Depuis pip 8.1

.. notes:
    lxml: sdist en 59sec vs <1sec pour lxml-3.7.3-cp36-cp36m-manylinux1_x86_64.whl

----

PEP 518 (mai 2016) - Dépendances de build
=========================================

- PEP 518 -- Specifying Minimum Build System Requirements for Python Projects

- Pour les setup.py nécessitant ``cffi``, ``cython``, etc

- ``setup_requires``

- Évite le ``pip install`` qui lance un ``setup.py install`` qui déclenche un ``easy_install``


.. notes:
    exemples de cffi, cython, etc

----

PEP 518 en pratique
===================

- pyproject.toml (au même niveau que le setup.py) pour définir les dépendances de build

.. code-block:: ini

    [build-system]
    requires = ["setuptools>=32.1.1", "wheel"]

- Valeur par défaut: ``["setuptools", "wheel"]``

- Évite l'appel sauvage à ``easy_install``

- Permet d'avoir des dépendances de builds différents pour différents projets

- Permet d'utiliser le cache de wheel de pip


.. code-block:: ini

    [tool.pkg_name_de_ton_outil_prefere]
    la_conf_de_ton_outil = "definie ici"

----

Pour aller plus loin
====================

S'affranchir de setuptools complètement pour les paquets simples (cf flit)

Autres PEP en brouillon:

- PEP 516 -- Build system abstraction for pip/conda etc

- PEP 517 -- A build-system independent format for source trees

----

Merci ! Des questions ?
=======================
