**Sommaire**

[Introduction](../README.md)  
1\. Création de l'infrastructure  
[2. Test de l'application](partie-2.md)  

# CREATION DE L'INFRASTRUCTURE

A l'aide de terraform, créez l'infrastructure décrite dans le schéma en introduction avec les caractéristiques suivantes (votre tfstate sera stocké dans le bucket **nuumfactory-terraform-backend** et s'appellera terraform-biglab-XX en remplaçant XX par votre digit).

Remplacez \<environnement\> par le trigramme d'environnement de votre choix (dev, rec, qal, etc...) :

**Internet gateway**

- Un tag Name avec pour valeur nuumfactory-biglab-igw-\<digit\>

**Nat gateways**

- Nat gateway 1 : Un tag Name avec pour valeur nuumfactory-biglab-natgw-1-\<digit\>
- Nat gateway 2 : Un tag Name avec pour valeur nuumfactory-biglab-natgw-2-\<digit\>

**Tables de routage**

- Sous-réseau public 1 : Un tag Name avec pour valeur nuumfactory-biglab-public-rtb-1-\<digit\>
- Sous-réseau public 2 : Un tag Name avec pour valeur nuumfactory-biglab-public-rtb-2-\<digit\>
- Sous-réseau privé 1 : Un tag Name avec pour valeur nuumfactory-biglab-private-rtb-1-\<digit\>
- Sous-réseau privé 2 : Un tag Name avec pour valeur nuumfactory-biglab-private-rtb-2-\<digit\>

**Load-balancer**

- Nom : nuumfactory-biglab-\<environnement\>-lb-\<digit\>
- Internal ? Non
- Forward les flux http (port 80) vers l'auto-scaling group
- Seuil healthy (healthcheck) = 2
- Seuil unhealthy (healthcheck) = 2
- Timeout (healthcheck) = 2 secondes
- Interval de healthcheck = 5 secondes

**Security groups**

- Load-balancer
    - Nom : nuumfactory-biglab-\<environnement\>-lb-sg-\<digit\>
    - Les flux HTTP en provenance de l'extérieur doivent être autorisés à atteindre le load-balancer
    - Un tag Name avec pour valeur nuumfactory-biglab-\<environnement\>-lb-sg-\<digit\>

- Auto scaling group
    - Nom : nuumfactory-biglab-\<environnement\>-ec2-sg-\<digit\>
    - Les flux HTTP en provenance du load-balancer seulement doivent être autorisés à atteindre les EC2 de l'auto scaling group
    - Un tag Name avec pour valeur nuumfactory-biglab-\<environnement\>-ec2-sg-\<digit\>

- Cluster memcached
    - Nom : nuumfactory-biglab-\<environnement\>-memcached-sg-\<digit\>
    - Les flux memcached (port 11211) en provenance des EC2 de l'auto-scaling group doivent être autorisés à atteindre les noeuds du cluster memcached
    - Un tag Name avec pour valeur nuumfactory-biglab-\<environnement\>-memcached-sg-\<digit\>

**Auto scaling group**

- AMI : ami-0f15e0a4c8d3ee5fe
- Type d'instance : t3.micro
- Associer une adresse IP publique aux EC2
- Chaque EC2 devra avoir un tag Name avec pour valeur nuumfactory-biglab-\<environnement\>-ec2-\<digit\>
- Sous-réseaux : sous-réseaux privés 1 et 2
- Type de health check : ELB
- Nombre minimum d'instances : 1
- Nombre maximum d'instances : 10
- User data : cf. suite

Chaque instance comprise dans l'auto scaling group devra exécuter au démarrage le script [**user_data.tftpl**](../assets/user_data.tftpl). Ce script contient 2 variables qui devront être remplacées par leur bonne valeur lors de l'éxécution de terraform.

**Cluster memcached**

- Nom du cluster : nuumfactory-biglab-\<environnement\>-memcached-\<digit\>
- Type de noeud : cache.t2.micro
- Sous réseaux du subnet group : sous-réseaux privés 3 et 4
- Nombre de noeuds : 2
- Port : 11211

Sentez-vous libre de créer autant de fichier .tf que vous le souhaitez, d'utiliser des variables, modules ou tout autre élément qui rendront votre code lisible, facilement maintenable et réutilisable.