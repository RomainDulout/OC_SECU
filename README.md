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

Les attaques réseau sont une première grande catégorie d'attaques qui peuvent être menées contre les objets IoT.

**Q.** Ces attaques se décomposent généralement en 4 à 5 grandes étapes, lesquelles ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://blog.f-secure.com/fr/les-5-phases-dune-cyber-attaque-le-point-de-vue-du-pirate/

Dans cette partie, nous allons plus particulièrement nous intéresser à deux attaques réseau courantes : les vulnérabilités de DNS et les vulnérabilités du protocole TCP/IP.

Note : Les VMs Ubuntu utilisées sont de base configurées en anglais. Pour remédier à cela et pouvoir utiliser un clavier Français, on faut vous rendre dans *Systems Parameters* puis *Text entry* puis ajouter la langue française ("*+*" *French*). Vous aurez ensuite la possibilité de changer de langue.

### 2.A Vulnérabilités de DNS

Basiquement, un serveur DNS (*Domain Name System*) permet de réaliser la conversion entre des noms de domaines (URL par exemple) et adresses IP.

Dans cette partie, après avoir lancé un serveur SDN, nous allons essayer de voir comment un attaquant pourrait modifier le comportement de ce serveur et rediriger les utilisateurs vers des destinations non voulues et potentiellement malicieuses.

#### Mise en place de l'environnement

Pour pouvoir mener à bien cette partie, il va tout d'abord être nécessaire de mettre en place l'environnement.

On va ici considérer 3 machines :

   Utilisateur       ------------------     Attaquant    ---------------   Server DNS
(IP : 10.0.2.18)                          (IP : 10.0.2.17)               (IP : 10.0.2.16)

Sur VirtualBox, il va donc falloir lancer 3 VM Ubuntu différentes, en définissant leurs IPs et en indiquant dans les préférences réseau "NAT Network".

Ensuite, il va falloir configurer la machine de l'utilisateur :

1. Indiquez dans le fichier */etc/resolvconf/resolv.conf.d/head* l'adresse du serveur DNS :

        *nameserver 10.0.2.16*

2. Rechargez la configuration afin qu'elle prenne en compte cette modification :

        *sudo resolvconf -u*
        
3. En utilisant la commande *dig google.fr*, vérifiez que cela a bien fonctionné.

Dans un troisième temps, il faudrait normalement mettre en place le serveur DNS local. Toutefois, ceci a déjà été réalisé sur ces VMs. Les deux principales étapes ayant été réalisées sont la configuration du serveur DNS BIND 9 et la mise en veille de DNSSEC (qui vise à empêcher les attaques de type *spoofing* qui pourraient être menées à l'encontre du serveur).




il va falloir mettre en place le serveur DNS local (machine serveur DNS)

**Q.** Contremesures

### 2.B Vulnérabilités du protocole TCP/IP


**Q.** Contremesures

## 3. Une seconde mise en pratique : Attaques logicielles

Dans cette partie, nous allons nous intéresser aux attaques de débordement de la mémoire tampon (*Buffer Overflow*). 

**Q.** Qu'est ce qu'une attaque par débordement de mémoire ? Expliquez le principe de façon claire. Quelles peuvent en être les conséquences de ces attaques (ie quel est l'objectif de l'attaquant) ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://aayushmalla56.medium.com/buffer-overflow-attack-dee62f8d6376.

Pour pouvoir mener à bien ces attaques par débordement de mémoire, nous allons devoir désactiver les différentes contremesures nativement présentes dans le système d'exploitation Ubuntu. Pour ce faire entrez les lignes de commande suivante :

```console
sudo sysctl -w kernel.randomize_va_space=0

sudo ln -sf /bin/zsh /bin/sh
```

### Tâche 1 : Lancement du shellcode

```console
#include <stdio.h>

int main() {
   char*name[2];
   name[0] = "/bin/sh";
   name[1] = NULL;
   execve(name[0], name, NULL);
   }

```

Créez un fichier correpondant au programme ci-dessus (qui est un *shellcode*) et lancez le en utilisant la commande suivante :

```
gcc -z execstack -o call_shellcode call_shellcode.c
```

*Note : Spécifier "-z execstack" permet d'indiquer que l'on souhaite que la pile puisse être exécutable. Ceci s'avèrera nécessaire pour que l'on puisse mener à bien les attaques par débordement de mémoire.* 

**Q.** Que semble permettre de faire le programme que vous venez de lancer ? Qu'est ce qu'est donc basiquement un *shellcode* ?

### Tâche 2 : Découverte du programme vulnérable



**Q. Contremesures**






## 4. Etude théorique : Attaques par canaux auxilaires (*Side-channel attack*)

Dans cette partie, au travers d'une étude théorique, nous allons nous intéresser aux attaques pouvant être menées au niveau de l'objet IoT lui même. Plus particulièrement, nous allons nous intéresser aux attaques par canaux auxiliaires. 

**Q.** Qu'est ce qu'une attaque par canaux auxiliaires ? Que cherche-t-on généralement à deviner ? Quelles valeurs peuvent être mesurées ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : http://www.bibmath.net/crypto/index.php?action=affiche&quoi=chasseur/canalauxiliaire

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




### 4.C Ouverture : Attaque différentielle par analyse de courant (*Differential Power Analysis*)

Alors que les attaques SPA et par injection de fautes font partie des attaques "simples", pouvant être exécutées dans un environnement peu protégé (ce qui correspond à bon nombre d'objets IoT), les attaques DPA font partie d'attaques plus complexes pouvait être exécutées contre des systèmes mieux protégés.

L'attaque DPA part de l'idée que la consommation de puissance est différente selon les états (bit à 0 ou 1) et les transitions (1->0 ou 0->1).

Toutefois, ces variations sont infinitésimales et peuvent être cachées par l'existence de bruit ou une erreur de mesure.

Par conséquent, l'approche DPA repose également sur une analyse statistique : l'attaquant va répéter plusieurs fois (1000, 10000) une même opération afin d'en déduire des tendances.

Ainsi, grâce à cette approche, l'attaquant pourra être en mesure (après un certain temps...) d'associer la mesure d'un instant donné à un état (ou une transition) et par conséquent d'en déduire la clé privée de l'objet IoT. 

Cette attaque, basée sur des variations très petites, et non liée à une erreur dans l'implémentation de l'algorithme de chiffrement, est bien plus puissance que les attaques SPA et d'injections de fautes. Toutefois, des contremesures sont possibles.

**Q.** Quelles contremesures pourraient être proposées contre ce genre d'attaques ?

## 5. Pour aller plus loin

Comme cela a déjà été indiqué, les expérimentations proposées dans les section **2** et **3** s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). 

**Q.** Indiquez, au delà des attaques réseau et logicielles, les questions auxquelles vous pourriez vous former grâce à ce projet.

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://seedsecuritylabs.org/Labs_20.04/ 

Au delà des contremesures actuellement implémentées, de nombreux organismes travaillent à la définition de nouvelles solutions qui pourraient permettre de renforcer la sécurité des objets connectés. Parmi les solutions potentielles, une technologie que nous avons étudié dans le cadre d'un autre TP est fortement mise en avant : la Blockchain.

**Q.** Indiquez pourquoi la Blockchain, grâce à ses caractéristiques, pourrait sembler être une solution pertinente pour la sécurisation des objets connectés. Donnez également des exemples de solutions existantes basées sur la Blockchain.

Pour répondre à cette question vous pourrez utiliser les informations présentées par : https://www.itransition.com/blog/blockchain-iot-security 

Pour votre culture personnelle (ou votre divertissement ?) un outil, évoqué dans l'introduction de ce TP, qu'il pourrait vous être utile de connaître est Shodan.

**Q.** Qu'est ce que Shodan (https://www.shodan.io/) ? Comment cet outil semble-t-il fonctionner ? Comment pourrait-il être utilisé ?



