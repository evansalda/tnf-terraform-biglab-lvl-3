# CREATION D'UNE INFRASTRUCTURE COMPLEXE AVEC TERRAFORM

**Sommaire**

Introduction  
[1. Création de l'infrastructure](pages/partie-1.md)  
[2. Test de l'application](pages/partie-2.md)   

## Introduction

L'objectif de ce lab est de créer une infrastructure complexe sur AWS avec **Terraform**.

### L'infrastructure

L'architecture de l'infrastructure est la suivante :

<p align="left">
<img src="img/infrastructure.jpg"/>
</p>

Dans cette architecture, nous retrouvons les éléments suivants :

- Un VPC hébergé dans une région qui vous sera attribuée
- 2 sous-réseaux publics répartis dans les availability zone A et B
- 4 sous-réseaux privés répartis dans les availability zone A et B
- Une internet gateway pour gérer le traffic entre l'extérieur et les sous-réseaux publics
- Une NAT gateway dans chaque sous-réseau public pour gérer les flux sortants des sous-réseaux privés 1 et 2
- Un load-balancer pour répartir les requêtes HTTP en provenance de l'extérieur vers les instances EC2 de l'auto-scaling group
- Un auto-scaling group dont les instances EC2 qui hébergent l'application web sont réparties sur les sous-réseaux privés 1 et 2.
- Un cluster memcached pour la gestion des sessions utilisateurs, composé de 2 noeuds répartis sur les sous-réseaux privés 3 et 4.

Cette architecture hébergera une application web basique qui sera installée sur les serveurs au travers d'user datas.

Vous trouverez ci-dessous les **adresses de sous-réseaux** que vous devrez utiliser (choisissez la région de votre choix) :

| Nom             | Prénom       | VPC         | PUBLIC SUBNET 1 | PUBLIC SUBNET 2 | PRIVATE SUBNET 1 | PRIVATE SUBNET 2 | PRIVATE SUBNET 3 | PRIVATE SUBNET 4 |
|-----------------|--------------|-------------|-----------------|-----------------|------------------|------------------|------------------|------------------|
| ARDOUIN         | Jean-Charles | 30.0.0.0/16 | 30.0.1.0/24     | 30.0.2.0/24     | 30.0.3.0/24      | 30.0.4.0/24      | 30.0.5.0/24      | 30.0.6.0/24      |
| BAUDRY          | Samuel       | 40.0.0.0/16 | 40.0.1.0/24     | 40.0.2.0/24     | 40.0.3.0/24      | 40.0.4.0/24      | 40.0.5.0/24      | 40.0.6.0/24      |
| BERNARD         | Christophe   | 50.0.0.0/16 | 50.0.1.0/24     | 50.0.2.0/24     | 50.0.3.0/24      | 50.0.4.0/24      | 50.0.5.0/24      | 50.0.6.0/24      |
| BOUCHETA        | Mohamed      | 60.0.0.0/16 | 60.0.1.0/24     | 60.0.2.0/24     | 60.0.3.0/24      | 60.0.4.0/24      | 60.0.5.0/24      | 60.0.6.0/24      |
| CUNHA DE ARAUJO | Jean-Paul    | 70.0.0.0/16 | 70.0.1.0/24     | 70.0.2.0/24     | 70.0.3.0/24      | 70.0.4.0/24      | 70.0.5.0/24      | 70.0.6.0/24      |
| RIVIER          | Nicolas      | 80.0.0.0/16 | 80.0.1.0/24     | 80.0.2.0/24     | 80.0.3.0/24      | 80.0.4.0/24      | 80.0.5.0/24      | 80.0.6.0/24      |
| TAVERNIER       | David        | 90.0.0.0/16 | 90.0.1.0/24     | 90.0.2.0/24     | 90.0.3.0/24      | 90.0.4.0/24      | 90.0.5.0/24      | 90.0.6.0/24      |