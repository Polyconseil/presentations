Toolchain Front, Reloaded
=========================

Ce que nous avons aujourd'hui
=============================

- blease-js: Une toolchain assez solide, mais figée.
- bluetils-js: Des helpers utiles, mais pas future-proof.
- Des applications systématiquement **Angular V1**

La quasi intégralité est en JavaScript "historique", ou ES5.

.. figure:: unbelievable.gif
    :height: 230px

----


Soucis constatés
================

- Blease-js est **lent**. La logique app.js/vendor.js devient caduque avec HTTP/2.
- Browserify gère très mal les **imports ES6**.
- Il nous a fallu créer des **polyfills** pour un certain nombre de libs.
- Il est **angular-specific**, à cause de angular-translate et des templates.
- Bluetils-js n'est **pas compatible ES6** et n'est pas **distribué proprement**.
- Toutes nos apps sont **forcées** d'utiliser AngularJS v1, angular-gettext a minima.

.. figure:: notbad.jpg
    :height: 200px


----


Le JS de demain
===============

- **Angular2** avec angular-cli, TypeScript, un loader SystemJS, i18n intégré
- React, **Virtual-Dom**, Leaflet-next, Redux, et VanillaJS :)
- PostCSS et CSS modules (`import 'component.css';`)
- ES6 puis ES7, pourquoi pas TypeScript ?


.. figure:: hothothot.gif
    :height: 200px


Pas choisir un framework: **permettre n'importe lequel, ou aucun !**
--------------------------------------------------------------------

Pas optimiser la toolchain: oublier la toolchain !
--------------------------------------------------


----


Quels sont nos besoins 
======================

- De quoi avoir des bibliothèques JS propres, ecrites dans un **langage au choix**
- De quoi développer **rapidement** une app sans passer trop de temps sur les tools.
- De quoi générer l'image de production d'une app facilement

  + Un bundle contenant tout le JS/CSS nécessaire à l'app 
  + Gestion des templates
  + Gestion des traductions
  + Demain, pouvoir générer un bundle HTTP/2 ready, dépendances sur CDN


Blease-JS est à revoir !

----


Les solutions
=============

**systematic** et **easygettext** (entre autres)

.. figure:: systematic.jpg
    :height: 430px

----


Systematic
==========

- Bibliothèques: squelette écrit, à l'aide de **Babel**, **mocha** et **isparta**
- Développer **rapidement**: utilisation de JSPM/SystemJS, cf Angular2
- Générer une image de prod: choix entre Webpack et JSPM. **JSPM won**.
- Gestion des traductions: **easygettext**

  + Similaire à angular-gettext et autres (jsi18next, ...)
  + Open-source, 100% testé, 100% ES6

- Cycle de vie des applications avec **systematic**:

  + Makefile court, 100% framework-agnostic; plus de dépendance sur blease
  + Même logique KISS d'utilisation d'outils CLI.
  + Gestion du developpement, test et release.

----


Tooling JS: Browserify / Webpack / JSPM
=======================================

Objectif: charger des modules JS dans une application.

Browserify gère très mal les **imports** ES6 ! Webpack supporte mieux, via des plugins.

JSPM propose bien plus que Webpack ou Browserify.

.. code-block:: bash

    jspm install polydev:bluetils-js
    jspm install ghpackage=github:owner/reponame

    import {githubStuff} from 'ghpackage';

    let someCss = import 'mycomponent.css!';
    const img = import 'some/image.png!image';
    import 'google Port Lligat Slab, Droid Sans !font';

----


Plus à propos de JSPM
=====================
- JSPM: Support de ES6 immédiat, gestion de packages via différentes stratégies.
- JSPM gère à la fois les imports AMD, CommonJS et ES6 directement; WebPack avec des plugins.
- JSPM accepte des sources depuis un CDN, depuis Github, NPM (plusieurs possibles)
- JSPM ne demande quasi aucune configuration ou plugin !
- SystemJS: Support de l'import de templates, polices, images, CSS, ...
- SystemJS: transpilation au **runtime** sans build manuel préalable. Avec dépendances.

.. figure:: magic.gif
    :height: 200px

----


Première application
====================

**alacarte**: on veut s'affranchir de l'existant.

- Full ES6 voire ES7
- Entierement framework-agnostic.
- Internationalisation ? Via marqueurs **easygettext**, puis soit custom, soit...
- **Import** du CSS et des templates directement en JS.

On tente le pari d'intégrer exactement ce qui nous plaît, sans
toucher jamais à la toolchain.

----


Améliorations à venir
=====================

- Private NPM Registry: Add LDAP authentication
- Utilisation de PostCSS avec les imports SystemJS
- Sourcemaps CSS dans le bundle généré
- Application à l'upload des apps dans Sentry 8

... et surement d'autres non encore remarquées ...

.. figure:: droopy.png
    :height: 300px


