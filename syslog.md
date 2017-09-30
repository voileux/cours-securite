# Journalisation <a id="titre"> </a>
[# 1 Pré-requis](prerequis)<br>
[## 1.1 Fonctionnalites](#fonctions)<br>
[## 1.2 Horodatage des évènements](#horodatage)<br>
[## 1.3 Synchronisations des horloges](#ntp)<br>
[## 1.4 Dimensionnement](#dim)<br>


[# 2 Architecture et conception](#archi)<br> 
[## 2.1 Résilience](#resilience)<br>
[### 2.1.1 Exportation des journaux](#export)<br>
[### 2.1.2 Centralisation des journaux](#centralisation)<br>

[## 2.2 Protection des données échangées](#protection)<br>
[### 2.2.1 Mode de transfert](#modetransfert)<br>
[### 2.2.2 Prétraitement des journaux](#pretraitement)<br>
[### 2.2.3 Fiabilisation des transfert des journaux](#fiabilisation)<br>
[### 2.2.4 Sécurisation du transfert des journaux](#secu)<br>
[### 2.2.5 Bande passante](#banwdith)<br>
[### 2.2.6 Utlisation du réseau d'administration](#reseauadmin)<br>

[## 2.3 Stockage](#stockage)<br> 
[### 2.3.1 Partition séparée](#partition)<br> 
[### 2.3.2 Arborescence](#arborescence)<br>
[### 2.3.3 Rotation](#rotation)<br>
[### 2.3.4 Archivage](#archive)<br>
[### 2.3.5 Protection des journaux](#protection)<br>

[## 2.4 Consultation](#consultation)<br>
[### 2.4.1 Choix d'un outil](#outil)<br>
[### 2.4.2 Définition des roles](#role)<br>

[## 2.5 Supervision espace disque](#sup)<br>


[# 3 Choix des Evènements](#event)<br> 
[## 3.1 Authentification](#authent)<br>
[## 3.2 Gestion des comptes et des droits](#gestioncompte)<br>
[## 3.3 Accès aux ressources](#accesressources)<br>
[## 3.4 Modification des stratégies de sécurités](#strategie)<br>
[## 3.5 Activité des processus](#processus)<br>
[## 3.6 Activité des systèmes](#systeme)<br>
[## 3.7 autres éléments](#autres)<br>




# 1 Pré-requis <a id='prerequis'></a>

## 1.1 Fonctionnalites <a id="fonctions"></a>

Les journaux doivent si possible être générés dans un **format interprétable**,
c’est-à-dire **compréhensible à la lecture** et **facilement analysable** de manière automatique par des
outils informatiques. Les évènements inscrits dans les journaux doivent être
composés de **champs fixes**  à la **grammaire bien définie**, celle-ci pouvant évoluer en fonction des versions des systèmes qui
génèrent les journaux. 

Un évènement doit contenir en particulier une **source identifiable** 
(un équipement, un utilisateur, un nom de processus ou plusieurs de ces éléments) 
permettant de déterminer avec le plus de précision possible son origine.

**R1: Utiliser des systèmes et des applicatifs disposant nativement d’une
fonctionnalité de journalisation est primordial. La prise en compte de cette fonction doit se faire
lors de tout démarche de conception et de développement."**

## 1.2 Horodatage des évènements <a id="horodatage"></a>

<li>La fréquence d’occurrence d’un évènement,
 (un evenement toute les heures vs ou toute les secondes )</li>

<li>le recoupement des informations,
 correlation d'évènements</li>

<li>tache cron, ou manuelle</li>

**R2: L’horodatage doit être activé pour l’ensemble des évènements afin de
permettre une meilleure exploitation des journaux.**


## 1.3 Synchronisations des horloges <a id="ntp"></a>

**R3: Les horloges des équipements doivent être synchronisées sur plusieurs
sources de temps internes cohérentes entre elles. Ces sources pourront elles-mêmes être
synchronisées sur plusieurs sources fiables externes, sauf pour les réseaux isolés.**

## 1.4 Dimensionnement <a id="dim"></a>

Stockage


**R4: Lors du dimensionnement des équipements, l’estimation de l’espace de
stockage nécessaire à la conservation locale des journaux est indispensable.**

Resistance à la charge 

[haut de page](#titre)

# 2 Architecture et conception <a id="archi"></a> 

## 2.1 Résilience <a id="resilience"></a>

### 2.1.1 Exportation des journaux <a id="export"></a>

- stockage inadapté 
  - taille du stockage 
  - perénité du stockage en cas de crash 

**R5: Les journaux doivent être automatiquement exportés sur une machine
différente de celle qui les a générés.**

### 2.1.2 Centralisation des journaux <a id="centralisation"></a>

simplification :  
<li>consultation</li>
<li>correlation</li>
<li>sauvegarde</li>

**R6: Les journaux de l’ensemble des équipements du système d’information
doivent être transférés sur un ou plusieurs serveurs centraux dédiés.**

**R7: Si le parc d’équipements qui génère des journaux est important, le serveur
central devra être redondé afin d’accroître la disponibilité du service de collecte de
journaux.**

**R8: Si la taille ou la typologie du système d’information le nécessite, une
approche hiérarchique pour l’organisation des serveurs de collecte doit être
retenue.**

## 2.2 Protection des données échangées <a id="protection"></a>

### 2.2.1 Mode de transfert <a id="modetransfert"></a>

2 modes :
- transfert temps réel
- transfert en temps différé

**R9: Si le contexte le permet, un transfert en temps réel des journaux sur les
serveurs centraux doit être privilégié.**

### 2.2.2 Prétraitement des journaux <a id="pretraitement"></a>

On veut avoir l'information brute sur le serveur central

**R10: Il est recommandé de ne pas effectuer de traitement sur les journaux
avant leur transfert.**


### 2.2.3 Fiabilisation des transfert des journaux <a id="fiabilisation"></a>

les liens réseaux ne sont pas fiable 

**R11: Il est recommandé d’utiliser des protocoles d’envoi de journaux basés sur
TCP pour fiabiliser le transfert de données entre les machines émettrices et les serveurs
centraux.**

### 2.2.4 Sécurisation du transfert des journaux <a id="secu"></a>

Le réseau n'est pas forcement un lien sécurisé

**R12: Il est recommandé d’utiliser des protocoles de transfert de journaux qui
s’appuient sur des mécanismes cryptographiques robustes en particulier lorsque les données
transitent sur de réseaux non maîtrisés.**

### 2.2.5 Bande passante <a id="banwdith"></a>

a mettre en correlation avec la quantité de log générée

**R13: Il est recommandé de bien contrôler la bande passante des flux réseau
utilisée pour transférer les journaux d’évènements.**

### 2.2.6 Utlisation du réseau d'administration <a id="reseauadmin"></a>

séparation des différents besoins réseau de la machine

**R14: Lorsque le besoin de sécurité pour le transfert des journaux est
important, il doit se faire sur un réseau d’administration dédié.**

**R15 : S’il n’existe pas de réseaux d’administration dans l’architecture pour
accueillir les serveurs de journalisation, ils doivent être placés dans une zone interne non exposée
directement à des réseaux qui ne sont pas de confiance (par exemple Internet).**

## 2.3 Stockage <a id="stockage"></a> 

### 2.3.1 Partition séparée <a id="partition"></a> 

ne pas perturber le fonctionnement des applications si trop de logs

**R16: Une partition disque doit être dédiée au stockage des journaux
d’évènements sur les équipements qui les génèrent ou qui les centralisent.**

### 2.3.2 Arborescence <a id="arborescence"></a>

meilleure exploitation des journaux 

**R17: Il est recommandé d’adopter une arborescence pour le stockage des
journaux d’évènements.**

### 2.3.3 Rotation <a id="rotation"></a>

operations réalisées : 
- séparation des fichiers (nouveau fichier avec date et heure) 
- compressions 
- effacement 

**R18: Une politique de rotation des journaux d’évènements doit être formalisée
et mise en œuvre sur l’ensemble des équipements du système de journalisation.**


### 2.3.4 Archivage <a id="archive"></a>

raisons:
- question fonctionnelle (retrouver des évèments très anciens)
- ordre légal et réglementaire 

**R19: La durée de conservation des fichiers de journaux étant soumise à des
contraintes légales et réglementaires, il convient d’en prendre connaissance pour définir les moyens
techniques nécessaire à l’archivage des journaux.**


### 2.3.5 Protection des journaux <a id="protection"></a>

droit d'accès

**R20: L’accès aux journaux doit être limité en écriture à un nombre restreint de
comptes ayant le besoin d’en connaître.**
**R21:Les processus de journalisation et de collecte doivent être exécutés par
des comptes disposant de peu privilèges.**


Fonction de sécurité avancées: 
<br>dans certains cas (jeux en ligne) les fichiers de logs doivent etre chiffré pour
assurer leur intégrité  et leur confidentialité pour renforcer leur valeur
probatoire

## 2.4 Consultation <a id="consultation"></a>

### 2.4.1 Choix d'un outil <a id="outil"></a>
la quantité d'information récolté sera facilité par un outil permettant la
correlation des évènements et leur utilisation

**R22: Un outil spécifique doit être utilisé pour une meilleure exploitation des
journaux présents sur les serveurs centraux, la détection d’évènements anormaux
en sera facilitée.**


### 2.4.2 Définition des roles <a id="role"></a>

plusieurs population peuvent avoir besoin d'accèder au journaux (BDA,
gestionnaire applicatif, admin sys, ...) 

**R23: Les comptes ayant accès à l’outil de consultation centralisée des
journaux doivent être associés à des rôles prédéterminés.**


## 2.5 Supervision espace disque <a id="sup"></a>

- les appli peuvent crasher si elles ne peuvent plus écrire de log 
- perte d'information 
- changement d'une habitude de journalisation -> incident de sécurité 
- ajout de nouveau équipement dans le SI -> prendre en compte l'espace sur le
  serveur central 

**R24: L’espace disque des équipements qui génèrent et stockent les journaux
doit être surpervisé.**

[haut de page](#titre)

# 3 Choix des Evènements <a id="event"></a> 

## 3.1 Authentification <a id="authent"></a>
- réussites et échec d'authentification
- utilisation des différents mécanismes d'authentification
- élévation de privilèges

## 3.2 Gestion des comptes et des droits <a id="gestioncompte"></a>
- ajouts/suppression des comptes/groupes/roles
- affectations/suppressions de droits aux comptes/groupes/roles
- modification des données d'authentification 

## 3.3 Accès aux ressources <a id="accesressources"></a>
- accès ou tentative d'acces en lecture/écriture/exécution aux ressources

## 3.4 Modification des stratégies de sécurités <a id="strategie"></a>
- éditions, applications, réinitialisation de configurations

## 3.5 Activité des processus <a id="processus"></a>
- démarrage/arrets
- dysfonctionnements 
- chargements/déchargements de modules

## 3.6 Activité des systèmes <a id="systeme"></a>
- démarrages/arrets
- dysfonctionnement/surcharge du système
- chargement /déchargements de modules
- activités matérielles (défaillances, connexions/déconnexions physiques ...) 

## 3.7 autres éléments <a id="autres"></a>
- niveau de sévérité
- niveau de verbosité
- fréquence d’émission, etc.

[haut de page](#titre)
