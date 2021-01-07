# TP : Introduction à la sécurité dans l'Internet des Objets (IoT)

Le but de ce TP est de vous offrir une petite introduction à la sécurité dans l'environnement IoT.

**Note : A la fin de la scéance, pensez à m'envoyer un compte-rendu (court) répondant aux différentes questions présentes dans ce TP (leo.mendiboure@univ-eiffel.fr)**

**Note : A condition que vous ayez installé/que vous installiez VirtualBox sur votre machine personnelle, il est tout à fait possible de réaliser ce TP sans se servir des machines de l'école.**

## 1. Petit retour sur les objets connectés

La sécurité des objets connectés apparait aujourd'hui comme un élément essentiel et ceci pour différentes raisons.Tout d'abord parce plusieurs centaines de millions d'objets circulent aujourd'hui et que ce nombre ne cesse d'augmenter. Il s'agit par conséquent d'un vecteur d'attaque important. Ensuite, parce que ces objets rencontrent différents problèmes de sécurité :
- un manque de supervision : peu ou pas de mises à jour de sécurité, peu de moyen de supervisions/détection des attaques, accès physique potentiel à l'objet ;
- un manque de ressources : peu de ressources à consacrer à la sécurité (difficile déploiement de solutions cryptographiques, absence de sécurité pour les environnements d'exécution), peu de ressources (stockage, calcul, communication) dans l'absolu ;
- une manque d'homogénéité : hétérogénéité architecturale (tant matérielle que logicielle), hétérogénéité des protocoles (tant en terme de communication que de sécurité).

Bien que ces objets connectés soient généralements associés à des gadgets dans l'imaginaire collectifs, comme l'on montré différentes attaquées passées ou actuelles (Jeep hack, Nest hack, Vtech hack, Mirai), la vulnérabilité de ces objets entraine des risques importants :
- attaques destructrices : ces attaques, potentiellement à grande échelle (Shodan), peuvent entraîner, par exemple, la déstabilisation de grands groupes industriels voire d'états (concurrents/ennemis ?) ;
- espionnage : captation de données personnelles, détournement de l'usage de capteurs (micros, caméras, etc.) ;
- sabotage : perturbation du fonctionnement d'un service (ville intelligente, transport, énergie, etc.) ;
- détournement de l'utilisation d'objets connectés : Botnets (DDoS, Spam, etc.).


**Q.1** Rappelez quels sont les éléments présents dans l'architecture IoT. Déduisez-en à quel niveau peuvent se produire les attaques contre cette architecture.  

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://iot-analytics.com/understanding-iot-security-part-1-iot-security-architecture/

**Q.2** Expliquez rapidement ce qu'est l'OWASP et indiquez quelles sont les 10 principales vulnérabilités identifiées pour l'environnement IoT.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://wiki.owasp.org/index.php/OWASP_Internet_of_Things_Project#tab=IoT_Top_10

Dans la suite de ce TP, aux travers de mises en pratique/analyses théoriques, nous nous focaliserons sur trois types d'attaques : matérielles (physiques), logicielles et réseau.

## 2. Attaques réseau et logicielles

Dans cette partie, nous allons mettre en pratique différents types d'attaques réseau/logicielles. Nous nous intéresserons également aux contre-mesures permettant de se prémunir contre ces différentes attaques.

Il est à noter que l'environnement utilisé ici est basé sur une plateforme développée par l'université d'Orléans (https://celene.univ-orleans.fr/course/view.php?id=2575) et l'Université de Manchester.

### 2.A Mise en place de l'environnement

#### VirtualBox et Netkit

Pour mener à bien ces expérimentations, nous allons utiliser deux outils principaux :
* VirtualBox (install pour Linux : https://www.virtualbox.org/wiki/Linux_Downloads)
* NetKit (install : https://www.brianlinkletter.com/installing-netkit/)

Il est à noter que pour faciliter l'installation, nous l'allons pas directement installer l'environnement NetKit sur les machines (sauf si vous souhaitez vous même réaliser l'installation) mais plutôt utiliser une machine virtuelle contenant l'ensemble des installations nécessaires. A l'adresse suivante (https://celene.univ-orleans.fr/mod/url/view.php?id=277542) vous pourrez récupérer cette machine virtuelle.

**Q.** Expliquez ce qu'est NetKit ? Expliquez également quel pourra être l'intérêt de cet outil dans ce contexte.

#### Lab d'expérimentation

Au delà de ces deux outils, nous allons également avoir besoin du lab Netkit contenant l'ensemble des fichiers nécessaires à la réalisation de ce TP. Il s'agit du dossier compressé *lab.tar.gz* accessible à la racine de ce repo GitHub.

Une fois la VM lancée (le mot de passe est le nom d'utilisateur), téléchargez ce dossier compressé et décompressez le.

A partir de ce moment, en lançant simplement la commande *lstart*, à la racine de ce dossier, il devrait vous être possible de lancer l'environnement d'expérimentation. Pour l'arrêter, il vous suffira simplement de lancer dans le même terminal la commande *lcrash*.

L'environnement qui va être émulé ici se compose au total de trois hôtes et de deux sous réseaux (*lana*, *lanb*) :
- alice (10.0.0.1; 10.0.0.0/8) est un hôte vulnérable ;
- bob (192.168.0.1; 192.168.0.0/24) est un second hôte vulnérable ;
- oscar est un utilisateur malveillant (10.0.0.2; 10.0.0.0/8).

Il est à noter que l'adresse de la passerelle sur sous réseau *lana* est 10.255.255.254.

#### Scapy

Un autre outil qui va s'avérer très important dans la suite de ces expérimentations est Scapy. Vous pourrez l'utiliser et le lancer en utilisant les commandes suivantes :

```console
git clone https://github.com/secdev/scapy.git
cd scapy
./run_scapy
```

**Q.** Expliquez ce qu'est Scapy (https://github.com/secdev/scapy) ? Expliquez également quel pourra être l'intérêt de cet outil dans ce contexte.

#### Wireshark

Tout au long des expérimentations, nous allons utiliser Wireshark pour analyser les échanges entre les différentes hôtes et en déduire les conséquences des attaques qui seront menées par oscar.

Depuis un terminal, la commande permettant d'accéder à un des sous réseaux créé par NetKit (*lana*, *lanb*) est : `vdump *nom_ss_reseau* | wireshark -i - -k &`

### 2.B Une première attaque : Empoisonnement des tables ARP

Il s'agit là d'une première attaque réseau envisageable. Les attaques réseau sont une première grande catégorie d'attaques qui peuvent être menées contre les objets IoT.

**Q.** Ces attaques se décomposent généralement en 4 à 5 grandes étapes, lesquelles ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://blog.f-secure.com/fr/les-5-phases-dune-cyber-attaque-le-point-de-vue-du-pirate/

**Q.** Rappelez quelle est l'utilité d'une table ARP et à quoi correspondent les ARP Query and Reply. Indiquez également quel pourrait être l'intérêt de s'attaquer à ces tables ARP (*ARP Spoofing/ARP Poisoning*).

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://fr.wikipedia.org/wiki/ARP_poisoning

L'objectif d'oscar, en utilisant Scapy, va ici être d'empoisonner la table ARP d'alice et de se faire passer pour sa passerelle 10.255.255.254. Ainsi, il recevra le trafic destiné à la passerelle.

Pour ce faire, oscar va devoir envoyer toutes les secondes à alice un paquet ARP forgé indiquant que son adresse MAC correspond à l'adresse MAC de la passerelle.

Pour parvenir à ceci, vous devrez utiliser Scapy qui permet l'envoi et la manipulation de paquets.

Par exemple, l'utilisation de Scapy dans le terminal d'oscar, et le lancement de la commande suivante dans Scapy `sr1(IP(dst="10.0.0.1")/ICMP()/"bonjour")` permettrait d'envoyer un ping à Alice et d'en afficher la réponse.

Pour créer la ligne permettant cette redirection, vous pourrez utiliser différentes commandes de Scapy, notamment (*getmacbyip, Ether, ARP*).

Vous pourrez également vous inspirer de la solution proposée par https://medium.com/datadriveninvestor/arp-cache-poisoning-using-scapy-d6711ecbe112 

**Q.** Indiquez la ligne de commande que vous avez utilisée pour empoisonner la table ARP d'alice. Indiquez également quels sont les mots clés permettant d'indiquer le nombre de message que l'on souhaite envoyer ainsi que la fréquence.

 À l'aide de Wireshark, vous pouvez vérifier que l'empoisonnement de la table ARP a bien fonctionné. Pour ce faire, observez le trafic avec et sans empoisonnement de la table ARP lorsque alce se connecte au service UDP echo de bob.
 

**Q.** Quelles différences observez vous ? A quoi correspond cette redirection ?

**Q.** Quelles contre-mesures peuvent être envisagées pour lutter contre ce genre d'attaques ? 

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://en.wikipedia.org/wiki/ARP_spoofing#Defenses

### 2.B Une seconde attaque : Déni-de-Service 

**Q.** Qu'est ce qu'une attaque de Déni-de-Service ? Quel est son objectif ? 

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://fr.wikipedia.org/wiki/Attaque_par_d%C3%A9ni_de_service

#### Smurf

Les attaques de type *Smurf* sont un premier type possible d'attaques de DoS.

**Q.** Quel est le principe de ces attaques ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://en.wikipedia.org/wiki/Smurf_attack

Créez au niveau d'oscar, une ligne de commande Scapy permettant de forcer alice à envoyer en boucle des requêtes ICMP à l'adresse de broacast 10.255.255.255. Comme indiqué plus tôt, `sr1(IP(dst="10.0.0.1")/ICMP()/"bonjour")` pourrait permettre l'envoi d'une requête ICMP. 

**Q.** Indiquez la ligne de commande permettant de mener à bien ce genre d'attaque.

**Q.** Quels sont les risques de ce genre d'attaques ? Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.

#### SYN Flood

Les attaques de type *SYN Flood* sont un second type possible d'attaques de DoS.

**Q.** Quel est le principe de ces attaques ?

Nous allons maintenant essayer de mener ce genre d'attaque à l'encontre d'alice.

Pour ce faire, il suffit à oscar de créer un paquet multi-couche destiné à alice :

```console
topt=[(‘Timestamp’, (10,0))]
p=IP(dst="...", id=1111,ttl=99)/TCP(sport=RandShort(),dport=[22,80],seq=12345,ack=1000,window=1000,flags=”S”,options=topt)/”SYNFlood”
ans,unans=srloop(p,inter=0.2,retry=2,timeout=6)
```

**Q.** A quoi servent à votre avis les éléments aléatoires de ce message (id, ttl) ?

Ainsi, avec ces quelques lignes, oscar va envoyer à alice un paquet toutes les 0.2secondes pendant 6 secondes. Il s'agit là d'un exemple simple correspondant à une attaque de type *SYN Flood*. Cette succession rapide de requêtes SYN pourrait entraîner une surcharge du système d'Alice et pourrait donc rendre impossible l'accès aux services d'alice (par exemple un service telnet).

**Q.** Quels sont les risques de ce genre d'attaques ? Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.


#### Ping of Death

Les attaques de type *Ping of Death* sont un troisième type possible d'attaques de DoS.

**Q.** Quel est le principe de ces attaques ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://en.wikipedia.org/wiki/Ping_of_death

Si oscar souhaite mener ce type d'attaque à l'encontre d'alice, avec Scapy, il lui suffira d'utiliser une commande du type `send(fragment(IP(dst=dip)/ICMP()/(‘X’*60000))`.

Lancez cette commande et regarder le wireshark de *lana*.

Comme vous pouvez le constater le paquet ICMP, étant fragmenté, peut être envoyé bien que sa taille soit supérieure à la taille maximale définie pour ce type de paquet. Toutefois, le réassemblage de ce paquet pourrait perturber le bon fonctionnement d'alice.

**Q.** Quels sont les risques de ce genre d'attaques ? Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://www.imperva.com/learn/ddos/ping-of-death/


#### Attaques de Déni-de-Service avancées

Aujourd'hui, les attaques modernes représentent un danger réel pour les entreprises. En effet, elle pourrait entraîner une perturbation de leur fonctionnement tant en interne qu'en externe. Ces attaques sont capables d'atteindre des débits très élevés de l'ordre de plusieurs millirs de Gb/s. Pour ce faire, ces attaques se basent généralement sur des approches distribuées et sur l'utlisation de réflecteur.

**Q.** En prenant l'exemple de l'attaque *Mirai*, expliquez ce que sont ces réflecteurs et comment fonctionnent ces attaques DDoS. Indiquez également quelles sont les contre-mesures qui peuvent être mises en place.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://www.imperva.com/blog/how-to-identify-a-mirai-style-ddos-attack/

### 2.C Une troisième attaque : Vol de session

#### TCP Hijacking

#### Man-in-the-Middle

### 2.D Une quatrième attaque : Prise de contrôle

#### Par dépassement de tampon

#### Telnetd remote exploit




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

Le programme vulnérable, présent dans le dossier *Part3*, est nommé *stack.c*.

**Q.** En analysant la fonction *bof*, expliquez pourquoi ce programme est vulnérable aux attaques par débordement de mémoire.

Nous allons maintenant compiler ce programme. Pour ce faire, il nous faut suivre les étapes suivantes :
1. Lancer la commande permettant de compiler le programme `gcc -o stack -z execstack -fno-stack-protector stack.c`
2. Transformer l'exécutable en un programme root : `sudo chown root stack` puis `sudo chmod 4755 stack`

### Tâche 3 : Exploitation de la vulnérabilité

Comme vous avez pu le constater en l'analysant, le programme *stack.c* lit le contenu d'un fichier nommé *badfile*. Par conséquent, notre objectif va être de créer un contenu pour le fichier badfile nous permettant d'exploiter le problème de débordement mémoire pour lancer un shellcode (et par conséquent de prendre le contrôle du terminal de la victime).




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

Il est à noter que les mises en pratique proposées dans les sections **2** et **3** s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). Ce projet, mondial, vise à favoriser l'apprentissage de la sécurité informatique.



Comme cela a déjà été indiqué, les expérimentations proposées dans les section **2** et **3** s'inspirent fortement des exercices pratiques proposés par le projet SEED (https://seedsecuritylabs.org/). 

**Q.** Indiquez, au delà des attaques réseau et logicielles, les questions auxquelles vous pourriez vous former grâce à ce projet.

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://seedsecuritylabs.org/Labs_20.04/ 

Au delà des contremesures actuellement implémentées, de nombreux organismes travaillent à la définition de nouvelles solutions qui pourraient permettre de renforcer la sécurité des objets connectés. Parmi les solutions potentielles, une technologie que nous avons étudié dans le cadre d'un autre TP est fortement mise en avant : la Blockchain.

**Q.** Indiquez pourquoi la Blockchain, grâce à ses caractéristiques, pourrait sembler être une solution pertinente pour la sécurisation des objets connectés. Donnez également des exemples de solutions existantes basées sur la Blockchain.

Pour répondre à cette question vous pourrez utiliser les informations présentées par : https://www.itransition.com/blog/blockchain-iot-security 

Pour votre culture personnelle (ou votre divertissement ?) un outil, évoqué dans l'introduction de ce TP, qu'il pourrait vous être utile de connaître est Shodan.

**Q.** Qu'est ce que Shodan (https://www.shodan.io/) ? Comment cet outil semble-t-il fonctionner ? Comment pourrait-il être utilisé ?



