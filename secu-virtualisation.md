# risque lié a la virtualisation <a id="titre"></a>
 

[Risque 1 : Risque accru de compromission des systèmes](#compromission)<br>
[Risque 2 : Accroissement du risque d’indisponibilité](#indispo)<br>
[Risque 3 : Fuite d’information par manque de cloisonnement](#cloisonnement)<br>
[Risque 4 : Complexification de l’administration et de la mise en œuvre](#administration)<br>
[Risque 5 : Complexification de la supervision](#supervision)<br>
[Risque 6 : Prolifération non souhaitée des données et des systèmes](#proliferation)<br>
[Risque 7 : Incapacité à gérer voire à comprendre les erreurs](#erreurs)<br>
[Risque 8 : Investigations post incident plus difficiles](#investigations)<br>

## Risque 1 : Risque accru de compromission des systèmes <a id="compromission"></a> 

1 hote regroupant plusieurs vm si l'hote est compromis est ce que tout les vm de l'hote sont compromises ?

mise a jour régulière de tout les composants
diminuer les surfaces d'attaque 


## Risque 2 : Accroissement du risque d’indisponibilité  <a id="indispo"></a> 

les ressources étant regroupé et partagé

un ressource défaillante peut engendrer une déffaillance de l'ensemble du système



## Risque 3 : Fuite d’information par manque de cloisonnement <a id="cloisonnement"></a> 

le cloisonnement au sein d'environnement différents les différentes applications n'inter-agissant pas entre elle

au niveau réseau par exemple, si 1 seul carte réseau transporte l'ensemble des flux, elle peut etre compromise et les flux plus etre étanche 



## Risque 4 : Complexification de l’administration et de la mise en œuvre <a id="adminsitration"></a> 
Nouvelles taches de gestions : 
 - ressources SAN/NAS
 - cluster compute 
 - sauvegarde 
 - création de vm/container a la volé 


## Risque 5 : Complexification de la supervision  <a id="supervision"></a> 

les opérations de supervision peuvent aussi s’avérer complexes, en particulier du fait du paradoxe qui existe entre la nécessité de cloisonnement des machines 
virtuelles et le souhait d’une vision d’ensemble lors des opérations de supervision.
 Compte-tenu des cloisonnements induits par la solution de virtualisation, il peut être difficile de tracer un événement ou une action de bout en bout. 



## Risque 6 : Prolifération non souhaitée des données et des systèmes  <a id="proliferation"></a> 

les containers/vm doivent etre par définition migrable et les données sont souvent dupliqué pour être facilement utilisable 


## Risque 7 : Incapacité à gérer voire à comprendre les erreurs   <a id="erreurs"></a> 

Les problèmes de fonctionnement et les erreurs peuvent être complexes à gérer techniquement dans 
une architecture s’appuyant sur une solution de virtualisation.
Par exemple, les erreurs qui pourraient survenir lors de l’arrêt puis la relance d’une instance seront 
soit rapportées au système hôte que l’instance quitte, soit au système hôte qui est en train de l’accueillir. 

Sans la prise en compte globale des erreurs d’un système s’appuyant sur la virtualisation, il se peut 
que des informations pertinentes permettant d’identifier leur cause soient perdues, ou a minima, que 
leur synthèse ne puisse pas être réalisée. Il convient donc de mettre en place une centralisation et une 
corrélation des journaux sur l’ensemble des systèmes. Cette corrélation pose évidemment des problème 
sidentiques à ceux identifiés précédemment pour la supervision.

## Risque 8 : Investigations post incident plus difficiles <a id="investigations"></a> 


la mémoire non utilisé peut etre réutiliser par d'autre instance et donc non analysable en cas de crash d'une instance 





