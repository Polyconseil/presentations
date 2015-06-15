Packager et déployer avec docker (pour nous)
============================================

----

Cadrage de contexte
===================

Un système d'information, c'est quoi ?

- Un **gros** tas de code (appellons-le SI)
- Un ou des serveurs (des ports ouverts qui communiquent quoi)
- Des tâches qui tournent en `background`
- En général, des bases de données
- Des appels vers les Internets.

La mise en production, c'est quoi ?

- Rendre ce tas de code utilisable hors du laboratoire !
- Le mettre à jour pendant que des gens l'utilisent

Comme tout ce qui est intéressant, c'est complexe :)

----

Pourquoi c'est compliqué ?
==========================

Des tas de bonnes raisons.

- La base de données doit correspondre à ce qu'attend le code
- La communications entre systèmes a probablement évolué

  + Nouveaux protocoles (ajouts/suppression d'endpoints)
  + Nouveaux contrats d'interface (REST/SOAP) ?

- Nous parlons de centaines de milliers de ligne de code... :[
- Et surtout... 24 SI et ~40 bibliothèques interdépendantes :'(
- Il faut pouvoir vérifier que tout fonctionne bien ensemble !

Et surtout: **il faut pouvoir revenir en arrière !**

----

Comment on s'y pren *ait*
=========================

Un processus de mise à jour assez lourd...

- Test puis packaging de chacun des composants d'un SI
- Re-packaging des tarballs en packages Debian interdépendants
- Installation de ces packages, puis relance des serveurs
- Le tout avec un max de doigts croisés

Et pour s'assurer encore un peu mieux:

- Des *CHANGELOGs* détaillés pour savoir quoi tester
- Vérifications *pre*-staging (relecture, tests, re-relecture)
- Vérifications *post*-staging (test en environnement maîtrisé)


----

Ok, ça marche à peu près. Sauf que.
===================================

- Plus la codebase augmente, moins on a d'**agilité**
- Plus la codebase augmente, plus le risque à la relecture augmente
- Le processus de packaging à lui seul mobilise une demi-douzaine de personnes
- Pire, il peut encourager les erreurs humaines...

  + Oubli d'une ligne dans un des changelogs: on ne sait plus quoi tester
  + Une subtile erreur non détectée par les tests peut parfois apparaître
  + Problème de copier/coller lors de la création d'un package Debian...

Bref, beaucoup de choses peuvent mal tourner.

Nous avons atteint un degré de complexité pour lequel une mise à jour
manuelle n'est **plus une option.**


----

A la recherche de la solution idéale
====================================

Beaucoup d'alternatives on été envisagées

En vrac: *ssh, pip, chroot, tar, slugs, VM complète...*

Nous avons besoin de:

- Quelque chose de SIMPLE: adieu le packaging !
- Qui n'embarque pas des artefacts de développement
- Qui gère les versions de chaque logiciel
- Fonctionne quel que soit l'appli (front/back)
- Configurable de l'extérieur
- Start/stop/rollback possible
- Permette des tests d'intégration automatisés


----

Hail the Lord! We have Docker.
==============================

Docker: technologie basée sur les LxC (cgroups + namespacing)

Docker a atteint le stade production-ready (1.0) courant 2014. Ouf.

- Permet l'isolation grâce aux Linux Containers
- Bundles: une application et toutes ses dépendances sont empaquetées !
- Configurable de l'extérieur (volumes)
- Contient les interfaçages (serveurs internes)
- Versioning efficace de chaque application (Registry + AUFS)

La seule solution qui répond à **tous nos besoins à la fois**.


----

Notre outil: Grocker !
======================

Outil interne... **pour l'instant** ;)

- Une première machine, dite de *compilation*

  - Installe le code et dépendances (dont celles de dev) d'une version d'un SI
  - Génère des **paquets minimaux** (Wheels, .tar.gz)

- Une seconde machine, dite d'*exécution*

  - Base Debian (Jessie) minimale avec python, nginx, uwsgi...
  - Contient tous les paquets (SI & dépendances) prêts à l'emploi;
  - Spécificité: un **entrypoint** préparé par nos soins.

    + Permet de lancer bash, un serveur, un worker...
    + *Contraint* ces derniers: droits particuliers, environnement...

La machine d'exécution est versionnée et ajoutée à notre registry.

----

Qu'avons-nous résolu ?
======================

- Mises à jour, rollbacks beaucoup plus rapides.
- Les changelogs sont uniquement informatifs, pas contractuels.
- Nos cheveux sont plus lisses sans packaging Debian.
- Effet boîte noire: nous ne faisons que présenter un port, peu importe le host.
- *En cours* mise en place de tests d'intégration automatisés !

Et un jour... Un jour... Une **MEP automatique** ??? (ETA 2024)

- Environnement de staging avec tests fonctionnels complets et automatiques
- Retour de données permanent sur la production
- Dès qu'il y a détection d'une dérive de la production, **rollback**
- Caractérisation automatique du problème auprès de l'équipe


----

Toutes mes confuses
===================

Pour l'absence de jolies images dans cette présentation.

Veuillez me poser mille questions.
