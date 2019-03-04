# la virtualisation <a id="titre"></a>
 

[Les Risques](#risques)<br>

[Risque 1 : Risque accru de compromission des systèmes](#compromission)<br>
[Risque 2 : Accroissement du risque d’indisponibilité](#indispo)<br>
[Risque 3 : Fuite d’information par manque de cloisonnement](#cloisonnement)<br>
[Risque 4 : Complexification de l’administration et de la mise en œuvre](#administration)<br>
[Risque 5 : Complexification de la supervision](#supervision)<br>
[Risque 6 : Prolifération non souhaitée des données et des systèmes](#proliferation)<br>
[Risque 7 : Incapacité à gérer voire à comprendre les erreurs](#erreurs)<br>
[Risque 8 : Investigations post incident plus difficiles](#investigations)<br>

[Les points de controles](#controles)<br>

## Les Risques <a id="risques"></a> 

### Risque 1 : Risque accru de compromission des systèmes <a id="compromission"></a> 

1 hote regroupant plusieurs vm si l'hote est compromis est ce que tout les vm de l'hote sont compromises ?

mise a jour régulière de tout les composants
diminuer les surfaces d'attaque 


### Risque 2 : Accroissement du risque d’indisponibilité  <a id="indispo"></a> 

les ressources étant regroupé et partagé

un ressource défaillante peut engendrer une déffaillance de l'ensemble du système



### Risque 3 : Fuite d’information par manque de cloisonnement <a id="cloisonnement"></a> 

le cloisonnement au sein d'environnement différents les différentes applications n'inter-agissant pas entre elle

au niveau réseau par exemple, si 1 seul carte réseau transporte l'ensemble des flux, elle peut etre compromise et les flux plus etre étanche 



### Risque 4 : Complexification de l’administration et de la mise en œuvre <a id="adminsitration"></a> 
Nouvelles taches de gestions : 
 - ressources SAN/NAS
 - cluster compute 
 - sauvegarde 
 - création de vm/container a la volé 


### Risque 5 : Complexification de la supervision  <a id="supervision"></a> 

les opérations de supervision peuvent aussi s’avérer complexes, en particulier du fait du paradoxe qui existe entre la nécessité de cloisonnement des machines 
virtuelles et le souhait d’une vision d’ensemble lors des opérations de supervision.
 Compte-tenu des cloisonnements induits par la solution de virtualisation, il peut être difficile de tracer un événement ou une action de bout en bout. 



### Risque 6 : Prolifération non souhaitée des données et des systèmes  <a id="proliferation"></a> 

les containers/vm doivent etre par définition migrable et les données sont souvent dupliqué pour être facilement utilisable 


### Risque 7 : Incapacité à gérer voire à comprendre les erreurs   <a id="erreurs"></a> 

Les problèmes de fonctionnement et les erreurs peuvent être complexes à gérer techniquement dans 
une architecture s’appuyant sur une solution de virtualisation.
Par exemple, les erreurs qui pourraient survenir lors de l’arrêt puis la relance d’une instance seront 
soit rapportées au système hôte que l’instance quitte, soit au système hôte qui est en train de l’accueillir. 

Sans la prise en compte globale des erreurs d’un système s’appuyant sur la virtualisation, il se peut 
que des informations pertinentes permettant d’identifier leur cause soient perdues, ou a minima, que 
leur synthèse ne puisse pas être réalisée. Il convient donc de mettre en place une centralisation et une 
corrélation des journaux sur l’ensemble des systèmes. Cette corrélation pose évidemment des problème 
sidentiques à ceux identifiés précédemment pour la supervision.

### Risque 8 : Investigations post incident plus difficiles <a id="investigations"></a> 


la mémoire non utilisé peut etre réutiliser par d'autre instance et donc non analysable en cas de crash d'une instance 





## Les Points de Controle <a id="controles"></a>

### Point 1 : L’architecture de la solution de virtualisation a été conçue en prenant en compte les éléments suivants : <br>

 - le niveau d’exigences en termes de sécurité d’une machine physique doit être au moins égal au niveau d’exigences du système invité ayant le besoin de sécurité le plus élevé ;<br>
 - une atteinte en intégrité d’un des systèmes invités sur une machine physique peut porter atteinte à la sécurité de tous ses systèmes invités ;<br>
 - le risque d’indisponibilité d’une application est plus élevé si elle est hébergée sur une machine virtuelle ;<br>
 - la migration non voulue des systèmes invités, de leurs applications et des données qu’ellestraitent, d’une machine physique à une autre peut conduire à une circulation non souhaitée desdonnées sur le réseau ;<br>

### Point 3:
 Les systèmes invités présents sur une même machine physique manipulent des données qui ont une sensibilité similaire ;

### Point 4 :
 Les systèmes invités présents sur une même machine physique appartiennent originellement à une même zone de confiance (Réseau d’entreprise interne, de production, de recherche et déve-loppement, etc.) ;

### Point 5:
Une carte réseau physique est utilisée par groupe de systèmes invités qui manipulent des données de même sensibilité, en particulier si aucun autre moyen complémentaire de protection des flux(tel le chiffrement) n’est prévu par ailleurs ;

### Point 6 :
 L’utilisation des ressources (processeur, mémoire, espace disque) par chaque machine virtuelle est limitée afin qu’aucune d’elles ne puisse monopoliser le système hôte au détriment des autres ;

### Point 7: 
Des règles strictes, précises et cohérentes concernant la migration manuelle ou automatique des systèmes invités, de leurs applications et des données traitées entre différentes machines physiques sont établies ;

### Point 8 : 
Un réseau est dédié pour l’administration et la supervision des systèmes hôtes en s’appuyantsur des moyens réseau (cartes réseau, commutateurs) distincts de ceux utilisés par les systèmes invités ;

### Point 9 : 
Les postes dédiés à l’administration et à la supervision des machines virtuelles sont correctement sécurisés. En particulier, ils ne permettent pas l’accès à Internet ;

### Point 10 : 
Les administrateurs des machines hôtes doivent s’authentifier nominativement, et leurs actionssont journalisées 

### Point 11 : 
Tous les éléments d’authentification (mots de passe, certificats) par défaut ont été supprimés ou modifiés ;

### Point 12 : 
La solution de virtualisation gère de manière adéquate le cloisonnement des données, y compris vis à vis des périphériques, par la mise en œuvre, entre autres, d’une IOMMU (Input/OutputMemory Management Unit) ;

### Point 13 : 
La solution de virtualisation ne diminue pas le niveau de sécurité intrinsèque des systèmes invités.Par exemple, elle ne doit pas leur donner accès à des fonctionnalités matérielles sur lesquelles reposent certains de leurs mécanismes de sécurité ;

### Point 14 : 
Les systèmes hôtes et les systèmes invités sont impérativement sécurisés, notamment en dur-cissant les systèmes d’exploitation et en maîtrisant leur configuration. Ceci impose une gestionrigoureuse des supports d’installation et des mises à jour ;

### Point 15 : 
Les politiques et moyens techniques de mise à jour des systèmes invités, du système hôte et dela solution de virtualisation sont clairement définis, en particulier les mécanismes appliquant et contrôlant les mises à jour de sécurité des systèmes s’ils accèdent à Internet ou sont accessibles depuis Internet ;

### Point 16 : 
La solution de virtualisation a été évaluée d’un point de vue de la sécurité. Les mécanismes de cloisonnement entre les machines virtuelles font partie de la cible de sécurité s’il s’agit d’une certification ;

### Point 17 : 
L’ensemble des matériels, des systèmes et des couches de virtualisation est supervisé. Cela imposeau minimum la journalisation des informations de virtualisation, la synchronisation temporelle des machines hôtes, des systèmes invités et des éléments actifs du réseau afin de pouvoir corrélerles journaux ;

### Point 18 : 
La politique de sécurité existante prend bien en compte tous les points spécifiques à la solution de virtualisation mise en place ;

### Point 19 : 
Des administrateurs réseau et système sont formés aux techniques de la virtualisation. Les administrateurs de la solution de virtualisation sont choisis parmi les plus expérimentés ;

### Point 20  
Les administrateurs des machines hôtes et ceux des systèmes invités sont distincts ;

### Point 21 : 
Le personnel assurant l’administration et la supervision fait si possible l’objet d’une enquête de sécurité (voire, en fonction du contexte, d’une habilitation à accéder à des données de niveaude classification supérieur à celui des données traitées par les systèmes qu’il administre et/ousupervise) ;

### Point 22 : 
Les machines virtuelles sont créées et administrées en respectant des procédures rigoureuses. Ces procédures empêchent la prolifération non maîtrisée des images représentant les machinesvirtuelles et la copie ou le vol de ces images. Elles permettent de gérer la mise à jour de sécuritéde ces images afin de garantir l’exécution des machines virtuelles dans leur version la plus à jour
