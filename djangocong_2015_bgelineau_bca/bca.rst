Chargez votre voiture grâce au web
==================================

----

La borne de charge
==================

.. figure:: bca.png
    :height: 450px

    badge -> branche -> charge


----

L'existant : SourceLondon
=========================


.. figure:: new_charge_point.png
    :height: 450px

    Remplacer l'existant pour améliorer la qualité.


----

Première architecture : PyQt
============================

PyQt semblait avoir pleins d'avantages :

- fait pour des interfaces lourdes
- robuste
- très utilisé

À l'usage, il nous a cependant semblé inadapté :

- API non pythonique
- non maîtrisé par l'équipe -> coût d'entrée pour tout nouveau développeur
- il impose une façon de faire
- gros overhead pour une fonctionnalité simple


----

Seconde architecture
====================

Finalement nous avons décidé de jouer sur nos forces : les UIs webs.

.. figure:: technos.png
    :height: 380px


----

Vue de détail
=============

.. figure:: architecture.png
    :height: 450px


----

L'orchestrateur
===============

.. figure:: main_workflow.png
    :height: 450px

    gestion séquentielle des événements extérieurs


.. ----

.. Interfaces AngularJS
.. ====================

.. - sans état, simple (KISS)
.. - écoute sur un websocket et change la route à chaque publication
.. - cas spécifique pour le lancement -> doit demander à l'orchestrateur
.. - un template par route
.. - purement statique


----

Tests
=====

Les tests sont effectués en lançant l'orchestrateur dans un thread séparé et en vérifiant les interfaces (MQ, Pub/Sub Redis).

Tous les tests sont donc purement fonctionnels de l'allumage d'une borne à son extinction.

.. code-block:: python

    def test_charge_begin(self):
        # The charge point is powered up in the setUp
        self.card_swiped('charge_begin')
        self.assert_view(ui.PARKING_CHARGE_INSTRUCTIONS_VIEW)
        self.outlet_plugged('T1')
        self.assert_view(ui.WAITING_FOR_RESPONSE)
        self.assert_view(ui.CHARGE_BEGIN_BYE_VIEW)
        self.assert_set_light('green')
        self.assert_start_charge('T1')
        # Timeout, go back to idle
        self.assert_view(view=ui.WELCOME_VIEW)
        # The charge point is powered down in tearDown


----

Périmètre des tests (1)
=======================

Le périmètre peut être modifié en changeant la définition des actions.

.. figure:: tests1.png
    :height: 430px


----

Périmètre des tests (2)
=======================

De l'orchestrateur seul aux APIs.

.. figure:: tests2.png
    :height: 430px


----

Périmètre des tests (3)
=======================

À la borne complète en utilisant Selenium.

.. figure:: tests3.png
    :height: 430px


----

Rétrospectives de l'architecture
================================

.. raw:: html
    
    <ul class="simple" style='list-style-type:none;'>
        <li><span style="color:green;">✔</span> modulaire</li>
        <li><span style="color:green;">✔</span> comportement prédictible et sûr (pas de threads)</li>
        <li><span style="color:green;">✔</span> technologies maîtrisées par l'équipe et plus léger que PyQt</li>
        <li><span style="color:green;">✔</span> parfaitement pythonique (et JSique)</li>
        <li><span style="color:green;">✔</span> visualisation à distance de l'interface (de façon synchronisée)</li>
        <li><span style="color:green;">✔</span> les tests sont indépendants et pérennes</li>
        <li><span style="color:red;">✘</span> l'orchestrateur rajoute des indirections et du code</li>
        <li><span style="color:red;">✘</span> le couplage orchestrateur - vue AngularJS est fragile</li>
        <li><span style="color:red;">✘</span> pas d'appels bloquants</li>
        <li><span style="color:red;">✘</span> saviez-vous qu'un swipe de droite à gauche dans chromium retourne en arrière ?</li>
    </ul>


----

Merci de votre attention
========================

Des questions ?
