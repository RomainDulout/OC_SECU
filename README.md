# TP : Introduction à la sécurité dans l'Internet des Objets (IoT)

Le but de ce TP est de nous offrir une petite introduction à la sécurité dans l'environnement IoT.

**Note : A la fin de la scéance, pensez à m'envoyer un compte-rendu (court) répondant aux différentes questions présentes dans ce TP (leo.mendiboure@univ-eiffel.fr)**


## 1. Petit retour sur les objets connectés

Si la sécurité des objets connectés apparait aujourd'hui comme un élément essentiel, c'est tout d'abord parce plusieurs centaines de millions d'objets circulent aujourd'hui et que ce nombre ne cesse d'augmenter. Il s'agit par conséquent d'un vecteur d'attaque important. C'est ensuite parce que ces objets rencontrent différents problèmes de sécurité :
- un manque de supervision : peu ou pas de mises à jour de sécurité, peu de moyen de supervisions/détection des attaques, accès physique potentiel à l'objet
- un manque de ressources : peu de ressources à consacrer à la sécurité (difficile déploiement de solutions cryptographiques, absence de sécurité pour les environnements d'exécution), peu de ressources (stockage, calcul, communication) dans l'absolu...
- une manque d'homogénéité : hétérogénéité architecturale (tant matérielle que logicielle), hétérogénéité des protocoles (tant en terme de communication que de sécurité)

Bien que ces objets connectés soient généralements associés à des gadgets dans l'imaginaire collectifs, comme l'on montré différentes attaquées passées ou actuelles (Jeep hack, Nest hack, Vtech hack, Mirai), la vulnérabilité de ces objets entraine des risques importants :
- attaques destructrices : ces attaques, potentiellement à grande échelle (Shodan), peuvent entraîner, par exemple, la déstabilisation de grands groupes industriels voire d'états (concurrents ?)
- espionnage : captation de données personnelles, détournement de l'usage de capteurs (micros, caméras, etc.)
- sabotage : perturbation du fonctionnement d'un service (ville intelligente, transport, énergie, etc.)
- détournement de l'utilisation d'objets connectés : Botnets (DDoS, Spam, etc.)


**Q.1** Rappelez quels sont les éléments présents dans l'architecture IoT. Déduisez-en à quel niveau peuvent se produire les attaques contre cette architecture.  

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://iot-analytics.com/understanding-iot-security-part-1-iot-security-architecture/

**Q.2** Expliquez rapidement ce qu'est l'OWASP et indiquez quelles sont les 10 principales vulnérabilités identifiées pour l'environnement IoT.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://wiki.owasp.org/index.php/OWASP_Internet_of_Things_Project#tab=IoT_Top_10

Dans la suite de ce TP (par manque de temps), nous nous focaliserons sur les attaques au niveau réseau et au niveau de l'objet IoT lui même (logicielles et physiques).

Il est à noter que les mises en pratique proposées dans les sections **2** et **3** s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). Ce projet, mondial, vise à favoriser l'apprentissage de la sécurité informatique.


## 2. Une première mise en pratique : Attaques réseau

### 2.A Empoisonnement du cache ARP

### 2.B 

## 3. Une seconde mise en pratique : Attaques logicielles

## 4. Etude théorique : Attaques par canaux auxilaires (*Side-channel attack*)

Dans cette partie, au travers d'une étude théorique, nous allons nous intéresser aux attaques pouvant être menées au niveau de l'objet IoT lui même. Plus particulièrement, nous allons nous intéresser aux attaques par canaux auxiliaires. 

**Q.** Qu'est ce qu'une attaque par canaux auxiliaires ? Que cherche-t-on généralement à deviner ? Quelles valeurs peuvent être mesurées ?

Pour répondre à cette question vous pourrez utiliser les informations présentées par : http://www.bibmath.net/crypto/index.php?action=affiche&quoi=chasseur/canalauxiliaire

### 4.A Attaque simple par analyse de courant (*Simple Power Analysis*)

Une première attaque par canaux auxiliaires possible, parmi les plus simples, est l'attaque nommée *Simple Power Analysis (SPA)*.

Cette attaque consiste à mesurer directement la consommation d'un circuit électrique. L'objectif de cette attaque (ainsi que des autres attaques présentées dans cette partie **4**) est de deviner la clé privée utilisée par un objet IoT.

Pour comprendre le fonctionnement de cette attaque, nous allons nous focaliser ici sur l'algorithme à clé publique RSA (Rivest–Shamir–Adleman), couramment utilisé dans l'environnement IoT.

Avec cet algorithme, la fonction utilisée pour le déchiffrement d'un message est la suivante :


**M=C<sup>d</sup> mod(N)**

où *M* correspond au message déchiffré, *C* au message chiffré, *d* à la clé privée de l'objet IoT et *N* à la multiplication de deux entiers premiers.

Ainsi, l'objectif d'un attaquant serait ici de déterminer la valeur de *d*.

Or, avec RSA, la méthode la plus efficace pour calculer l'exponentiation d'un entier par un autre est la méthode dite d'exponentiation rapide :

_____
**Objectif, calculer d :**

1) Calcul de T, la décomposition binaire de d

d=d<sub>n</sub>d<sub>n-1</sub>...d<sub>1</sub>d<sub>0</sub><sup>2</sup>

2) Définir la valeur du résultat T

T <- C
    
3) Calculer l'exponentiation
  
Pour i allant de n-1 à 0:
  si d<sub>i</sub>=0:
  
   T <- T x T (carré)
      
  si d<sub>i</sub>=1:
        
   T <- T x T (carré)
        
   T <- T x C (multipication)

4) renvoyer T
____

**Q.** Si vous étiez un attaquant, en considérant l'algorithme ci-dessus, comment pourriez vous être en capacité de deviner la clé de l'objet IoT ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées aux slides 25 et 26 dans https://www.emse.fr/~nadia.el-mrabet/Presentation/Cours5_SCA.pdf

**Q.** Pour éviter ce problème et offrir une solution "à consommation constante", que le bit traité soit un 0 ou un 1, quelle contremesure pourriez vous proposer ?

### 4.B Attaque par injection de fautes 

Une contremesure que vous pourriez avoir introduit dans la partie précédente pourrait consister à introduire une opération factice lorsque le bit est 0 à :

_____
  si d<sub>i</sub>=0:
  
   T <- T x T (carré)
   
   U <- T x C (multiplication)
      
  si d<sub>i</sub>=1:
        
   T <- T x T (carré)
        
   T <- T x C (multipication)
_____

Ainsi, grâce à cette approche, la solution garantit une consommation énergétique constante (que le bit soit à 1 ou 0) sans impacter le résultat final, rendant l'attaque SPA impossible.

Toutefois, cette solution est imparfaite. En effet, elle pourrait être sensible aux attaques dites d'injection de fautes.

Ces attaques consistent à perturber le fonctionnement du système durant l'exécution de l'algorithme de chiffrement : sur tension, surchauffe, etc. 

Ainsi, si l'attaquant introduit des fautes au moment du calcul de l'exponentiation, il pourra être en mesure de déterminer si des opérations sont factices ou non :

- si l'attaquant perturbe le fonctionnement du système au moment d'une opération factice (multiplication dans le cas où le bit est à 0), le résultat final ne sera pas perturbé par cette attaque. Ainsi, le message sera correctement déchiffré ;

- si l'attaquant perturbe le fonctionnement du système au moment d'une opération non factice (multiplication dans le cas où le bit est à 1), le résultat final sera directement impacté par cette attaque. Ainsi, le message sera déchiffré de façon incorrecte.

Par conséquent, cette attaque par injection de fautes, permet d'attaquer un système protégé uniquement contre les attaques SPA et de déterminer la valeur de la clé privée de l'objet IoT.

**Q.** Quelle contremesure pourrait être proposée pour résoudre ce problème ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées au slide 37 dans https://www.emse.fr/~nadia.el-mrabet/Presentation/Cours5_SCA.pdf




### 4.C Attaque différentielle par analyse de courant (*Differential Power Analysis*)

## 5. Pour aller plus loin

Comme cela a déjà été indiqué, les expérimentations proposées dans les section **2** et **3** s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). 

**Q.** Indiquez, au delà des attaques réseau et logicielles, les questions auxquelles vous pourriez vous former grâce à ce projet.

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://seedsecuritylabs.org/Labs_20.04/ 

Au delà des contremesures actuellement implémentées, de nombreux organismes travaillent à la définition de nouvelles solutions qui pourraient permettre de renforcer la sécurité des objets connectés. Parmi les solutions potentielles, une technologie que nous avons étudié dans le cadre d'un autre TP est fortement mise en avant : la Blockchain.

**Q.** Indiquez pourquoi la Blockchain, grâce à ses caractéristiques, pourrait sembler être une solution pertinente pour la sécurisation des objets connectés. Donnez également des exemples de solutions existantes basées sur la Blockchain.

Pour répondre à cette question vous pourrez utiliser les informations présentées par : https://www.itransition.com/blog/blockchain-iot-security 

Pour votre culture personnelle (ou votre divertissement ?) un outil, évoqué dans l'introduction de ce TP, qu'il pourrait vous être utile de connaître est Shodan.

**Q.** Qu'est ce que Shodan (https://www.shodan.io/) ? Comment cet outil semble-t-il fonctionner ? Comment pourrait-il être utilisé ?



