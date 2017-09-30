# Securité des applications WEB <a id="titre"></a>
[1.Eléments d'Architecture](#architecture)<br>
[1.1 Défense en profondeur](#defprof)<br>
[1.3 Composants logiciels](#complog)<br>
[1.4 Mécansime d'administration](#mecadmin)<br>
[1.5 Disponibilité](#dispo)<br>
[1.6 Moindre Privilège](#moindpriv)<br>
[1.7 Fuites d'informations](#fuitinfo)<br>

[2 Code Applicatif du Site](#code)<br>
[2.1 Gestion des entrées](#gestionentre)<br>
[2.1.a Injection SQL](#SQL)<br>
[2.1.b XSS](#XSS)<br>
[2.1.c Redirections](#redirection)<br>
[2.1.d Inclusions de fichiers](#includfile)<br>
[2.1.e Autres Injections](#otherinject)<br>

[2.2 Logique Applicative](#logapp)<br>
[2.2.a Gestion de sessions](#session)<br>
[2.2.b Stockage des mot de passe](#password)<br>
[2.2.c Protection contre les requetes illégitimes](#illreq)<br>
[2.2.e Inclusion de contenus externes](#extcont)<br>


[3 Surveillance](#Surveillance)<br>




# 1.1 Architecture <a id="architecture"></a> 

## 1.1 défense en profondeur <a id="defprof"></a>

privilègier des architectures n-tiers avec des mécanismes de sécurité entre les
tiers <br>
ne pas focaliser la sécurité sur le tier de présentation 

isoler le plus possible les tiers

**R1: L’architecture matérielle et logicielle du site web et de son infrastructure
d’hébergement doit respecter le principe de défense en profondeur.**

## 1.2 Composant logiciels  <a id="complog"></a>

vérifier les plugins et librairies utilisées 

**R2 : Les composants applicatifs employés doivent être limités au strict
nécessaire.**

**R3: Les composants applicatifs employés doivent être recensés et maintenus à
jour.**

## 1.3 Mécanisme d'administration <a id="mecadmin"></a>

protéger toujours l'interface d'administration par du SSL et une authent et au
moins limiter cet accès qu'a certaine d'adresse IP identifiée. 


**R4: L’administration d’un site web doit se faire via des protocoles
sécurisés.**

**R5: L’accès aux mécanismes d’administration doit être restreint aux seuls
postes d’administration autorisés.**

**R6: Les administrateurs doivent être authentifiés de manière sûre.**


## 1.4 Disponibilite <a id="dispo"></a>
la protection du site en disponibilte est un problème délicat<br>
le maintient à jour des différents élements logiciels et une bonne gestion de la
complexité de traitement pouvant etre déclenché par les requetes utilisateurs peuvent suffir 

on peut mettre en place des mecanismes de répartition de charge -> HAProxy

lié au attaque DDOS, -> on peut faire appel a des CDN 
sinon fail2ban permet de deceler des comportements simples mais suspects. 
Attention aux faux positif 


## 1.5 Moindre privilège <a id="moindpriv"></a> 

**R7 : Le principe de moindre privilège doit être appliqué à l’ensemble des
éléments du système.** 

ne pas faire tourner tout en root, utiliser au mieux des comptes applicatifs, et
cloisonné au maximum les process 

Ne pas garder les fichiers d'install, les documentations et les fichers de
config d'exemple accessible.

**R8: Les fichiers pouvant être servis aux clients doivent être limités au
strict nécessaire.**

Pour les BDD, les comptes applicatifs n'ont aucune raison de pouvoir modifier
le schémas de la base.  il faudra aussi restreindre les accès aux UPDATE, DELETE,
et INSERT. Généralement le SELECT suffit aux applications. 

On pourra aussi utiliser des comptes de connexion a la BDD différent en fonction
des profil de clients ou des actions sur la base de données. 

permet de limiter les failles XSS 

**R9: Les droits sur la base de données doivent être gérés finement pour
mettre en oeuvre le principe de moindre privilège.**

## 1.6 Fuites d'informations <a id="fuitinfo"></a> 
limité les informations fourni par l'application 

Parmi les points participant de cette mesure, on peut citer :
<br>– la suppression des balises « meta » dans l’en-tête HTML lorsque celles-ci
indiquent le logiciel ayant généré la page ;<br>
– la suppression des éléments visibles sur la page indiquant les outils (CMS,
éditeur, etc.) utilisés ;<br>
– la limitation en production des informations de débogage dans les messages
d’erreur (en évitant par exemple de fournir la requête SQL qui a généré une erreur) ;<br>
– l’utilisation de pages d’erreurs personnalisées pour ne pas reposer sur les
pages par défaut facilement reconnaissables ;<br>
– dans certains cas, l’utilisation de l’erreur 404 générique plutôt que d’une
erreur 401, 403, 405, etc. pour éviter de révéler trop d’informations sur le fonctionnement ou le
contenu en accès limité du site ;<br>
– la banalisation des en-têtes HTTP qui peuvent fournir des informations de
version trop précises sur le serveur ou le système d’exploitation employés ;<br>
– la désactivation du listage des répertoires n’ayant pas explicitement d’index ;<br>
– le rejet, en production, des requêtes HTTP TRACE qui n’ont d’intérêt qu’en phase de débogage<br>

**R10: Limiter les renseignements fournis sur le fonctionnement technique du
site web.**

# 2.Code Applicatif du Site <a id="code"></a> 

**R11: Les traitements doivent tous être faits du côté du serveur. Les entrées
en provenance des clients ne doivent pas être considérées comme fiables et par conséquent, aucune
vérification ne doit être déléguée aux client**

Ainsi, pour illustrer ce propos, si le code Javascript exécuté coté client peut
faire certains contrôles à des fins d’ergonomie (pour signaler une probable faute de frappe dans un champ
par exemple), il ne faut en aucun cas se contenter de ce contrôle. Il faut au contraire partir du
postulat que le code client a pu ne pas s’exécuter (javascript désactivé ou requêtes malveillantes).

l'attaquant a un accès total à la machine qu'il utilise et peut donc modifier
toute les informations qu'il envoi. 

entete, champ caché de formulaire, calcul effectué par JS

## 2.1 Gestion des entrées <a id="gestionentre"></a>

### 2.1.a Injection SQL <a id="SQL"></a>

**R12: Les requêtes adressées à la base de données doivent être faites au
moyen de requêtes préparées fortement typées ou par l’intermédiaire d’une couche d’abstraction
assurant le contrôle des paramètres.
Dans les (rares) cas où cette approche serait impossible, il convient d’apporter
un soin particulier à s’assurer que les données manipulées ne comportent pas de
caractères spéciaux (au sens du SGBD) sans échappement et ont bien la forme
attendue.**

### 2.1.b XSS: Cross Site Scripting <a id="XSS"></a> 

injection de code malveillant dans l'application 

**R13 : Les données externes employées dans quelque partie que ce soit de la
réponse envoyée au client doivent avoir fait l’objet d’un « échappement » adapté au contexte
d’interprétation. Il convient en outre de vérifier qu’elles ont effectivement la forme attendue
lorsque celle-ci est connue.**

### 2.1.c Redirections

redirect pour authentification 

**R14 : Favoriser les redirections statiques plutôt que d’employer des
redirections controlées par des données externes.**

**R15: Pour les redirections dynamiques, adopter un fonctionnement en liste
blanche en vérifiant que les URL visées soient légitimes**

Cette dernière recommandation peut être mise en oeuvre de différentes manières.
Si la liste des cibles de redirections est finie et connue a priori, il est possible d’indexer
celle-ci par des étiquettes ou des indices numériques et d’utiliser uniquement ces indices pour les désigner.
Un attaquant ne pourra pas rediriger le client vers le site de son choix, les redirections se feront
nécessairement dans la liste.
Dans le cas contraire, il est nécessaire de vérifier que l’URL visée a la forme
attendue, par exemple au moyen d’une expression rationnelle. Il est particulièrement important de
s’assurer que le nom de domaine visé fait bien partie de la liste blanche des domaines vers lesquels le
site est susceptible de produire une redirection


### 2.1.d Inclusions de fichier

vérifier les contenus des fichiers recuperer, 

**R16: L’inclusion de fichiers dont le nom ou le chemin d’accès dépend d’une
donnée externe ne doit pas être employée.**

### 2.1.e Autres injections 

**R17: Il faut recourir au fonctionnement par liste blanche ou à un traitement
rigoureux des données externes à chaque fois qu’elles sont employées.**

Ainsi, il faut prendre garde par exemple aux chaînes qui seront employées dans
une requête LDAP, dans une expression XPath, dans un document XML, une commande shell, etc

## 2.2 Logique applicative <a id="logapp"></a>

### 2.2.a. Gestion de sessions <a id="session"></a>
utiliser des mécanismes de gestion de session déja réalisé 
ne pas tenter de fabriquer le votre 

**R18 : Les identifiants de session doivent être aléatoires et d’une entropie
d’au moins 128 bits.**

utiliser les flags HTTPOnly et Secure 

Secure : permet de demander au navigo de n'envoyer les cookies que de manière
securisé (attention cela reste dépendant de la configv du navigo) 
HTTPonly: permet de n'acceder au cookiee que par HTTP 


### 2.2.b Stockage des mots de passe <a id="password"></a>
préférer une authent par SSO 

**R19: Les mots de passe ne doivent pas être stockés en clair mais dans une forme
transformée par une fonction cryptographique non réversible**

**R20: La transformation des mots de passe doit faire intervenir un sel aléatoire**

### 2.2.c Protection contre les requets illégitimes  <a id="illreq" ></a>

les attaques de types XSRF 

utilisation du champ Referer indiquant l'origine de la requete 

Une approche un peu plus complexe mais plus solide est de faire usage de «jetons » aléatoires.
Lors de la génération d’une page contenant un formulaire sensible, on génére et
stocke côté serveur une valeur aléatoire que l’on place dans un champ invisible du formulaire considéré.
Lorsque le formulaire est validé, on n’accepte l’entrée que si le jeton est présent et correspond
effectivement à un jeton généré récemment et non encore utilisé par une autre requête. De cette manière, on
réduit drastiquement les opportunités d’attaques. La même logique est bien évidemment transposable aux
liens générés avec un certain nombre de variables en GET auxquelles on peut ajouter une variable «
jeton"

**R21: Pour les actions sensibles, mettre en place des mécanismes permettant de
s’assurer de la légitimité de la requête.**

### 2.2.d Inclusion de contenus externes <a id="extercont"></a>

**R22: Limiter au strict nécessaire les inclusions de contenus tiers. Effectuer
au moins quelques contrôles élémentaires pour s’assurer de la fiabilité du
fournisseur de contenu**

### 2.2.e Passage a un paradigme statique <a id="static"></a>

beaucoup de site n'ont pas besoin d'etre "calculé" et peuvent facilement etre
servi en statique. 

**R23: Privilégier un paradigme statique chaque fois que c’est possible.**



# 3 Suveillance

**R24: Le site web doit être régulièrement parcouru pour déceler toute
anomalie.**

Attention les pages modifiées ne peuvent pas forcement etre visible de
l'interieur 

utiliser des outils permettant de vérifier l'intégrité de l'arborescence du site 
mtree, rkhunter  


