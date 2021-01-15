# TP : Introduction à la sécurité dans l'Internet des Objets (IoT)

Le but de ce TP est de vous offrir une petite introduction à la sécurité dans l'environnement IoT.

**Note : A la fin de la scéance, pensez à m'envoyer un compte-rendu (court) répondant aux différentes questions présentes dans ce TP (leo.mendiboure@univ-eiffel.fr)**

**Note : A condition que vous ayez installé/que vous installiez VirtualBox sur votre machine personnelle, il est tout à fait possible de réaliser ce TP sans se servir des machines de l'école.**

**Note** : sur les machines de l'école pensez à entrer la commande `mount mondzouk-server3:/media/images/Archives/Debian8-mirror /media/mirror`

## 1. Petit retour sur les objets connectés

La sécurité des objets connectés apparait aujourd'hui comme un élément essentiel et ceci pour différentes raisons. Tout d'abord parce plusieurs centaines de millions d'objets sont en circulation et que ce nombre ne cesse d'augmenter. Il s'agit par conséquent d'un vecteur d'attaque important. Ensuite, parce que ces objets rencontrent différents problèmes de sécurité :
- un manque de supervision : peu ou pas de mises à jour de sécurité, peu de moyen de supervisions/détection des attaques, accès physique potentiel à l'objet ;
- un manque de ressources : peu de ressources à consacrer à la sécurité (difficile déploiement de solutions cryptographiques, absence de sécurité pour les environnements d'exécution), peu de ressources (stockage, calcul, communication) dans l'absolu ;
- une manque d'homogénéité : hétérogénéité architecturale (tant matérielle que logicielle), hétérogénéité des protocoles (tant en terme de communication que de sécurité).

Bien que ces objets connectés soient généralements associés à des gadgets dans l'imaginaire collectif, comme l'on montré différentes attaques passées ou actuelles (Jeep hack, Nest hack, Vtech hack, Mirai), la vulnérabilité de ces objets entraine des risques importants :
- attaques destructrices : ces attaques, potentiellement à grande échelle (Shodan), peuvent entraîner, par exemple, la déstabilisation de grands groupes industriels voire d'états (concurrents/ennemis ?) ;
- espionnage : captation de données personnelles, détournement de l'usage de capteurs (micros, caméras, etc.) ;
- sabotage : perturbation du fonctionnement d'un service (ville intelligente, transport, énergie, etc.) ;
- détournement de l'utilisation d'objets connectés : Botnets (DDoS, Spam, etc.).


**Q.1** Rappelez quels sont les éléments présents dans l'architecture IoT. Déduisez-en à quel niveau peuvent se produire les attaques contre cette architecture.  

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://iot-analytics.com/understanding-iot-security-part-1-iot-security-architecture/

**Q.2** Expliquez rapidement ce qu'est l'OWASP et indiquez quelles sont les 10 principales vulnérabilités identifiées pour l'environnement IoT.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://wiki.owasp.org/index.php/OWASP_Internet_of_Things_Project#tab=IoT_Top_10

Dans la suite de ce TP, aux travers de mises en pratique/analyses théoriques, nous nous focaliserons sur trois types d'attaques : matérielles (physiques), logicielles et réseau.

## 2. Mise en pratique : Attaques réseau et logicielles

Dans cette partie, nous allons mettre en pratique différents types d'attaques réseau/logicielles. Nous nous intéresserons également aux contre-mesures permettant de se prémunir contre ces différentes attaques.

### 2.A Mise en place de l'environnement

Il est à noter que l'environnement utilisé ici est basé sur une plateforme développée par l'université d'Orléans (https://celene.univ-orleans.fr/course/view.php?id=2575) et l'Université de Manchester.

#### Netkit

Pour mener à bien ces expérimentations, nous allons utiliser un outil principal : NetKit (install :https://www.netkit.org/).

Pour réaliser l'installation de cet outil sur votre machine, il vous faudra récupérer l'ensemble des sources (3 dossiers compressés !) à l'adresse suivante (https://www.netkit.org/) puis suivre les trois étapes décrites dans (https://www.brianlinkletter.com/installing-netkit/).

Une fois les étapes réalisées, grâce au fichier `./check_configuration.sh`, vérifiez que l'installation a correctement été réalisée

Il est à noter que si vous souhaitez réaliser ce TP directement sur votre machine personnelle (et non les ordinateurs de l'école), vous pouvez le faire en utilisant une machine virtuelle contenant l'ensemble des installations nécessaires. A l'adresse suivante (https://drive.google.com/file/d/1b0oJ5-UMHXhtMqb2tG4vxp8t0dC2ZUqN/view?usp=sharing), vous pourrez récupérer cette machine virtuelle.

**Q.3** Expliquez ce qu'est NetKit. Expliquez également quel pourra être l'intérêt de cet outil dans le cadre de ce TP.

#### Lab d'expérimentation

Au delà de cet outil, nous allons également avoir besoin du lab Netkit contenant l'ensemble des fichiers nécessaires à la réalisation de ce TP (ie l'environnement de simulation utilisé). Il s'agit du dossier compressé *lab.tar.gz* accessible à la racine de ce repo GitHub.

Téléchargez ce dossier compressé et décompressez le.

A partir de ce moment, en lançant simplement la commande *lstart*, à la racine de ce dossier (Attention, *lstart* est un fichier binaire de NetKit, le chemin menant à ce fichier **.../netkit/bin** doit être précisé !), il devrait vous être possible de lancer l'environnement d'expérimentation. 

Pour l'arrêter, il vous suffira simplement de lancer dans le même terminal la commande *lcrash* (*lclean* permettant de nettoyer la config).

L'environnement qui va être émulé ici se compose au total de trois hôtes et de deux sous réseaux (*lana*, *lanb*) :
- alice (10.0.0.1; 10.0.0.0/8) est un hôte vulnérable ;
- bob (192.168.0.1; 192.168.0.0/24) est un second hôte vulnérable ;
- oscar est un utilisateur malveillant (10.0.0.2; 10.0.0.0/8).

Il est à noter que l'adresse de la passerelle du sous réseau *lana* est 10.255.255.254.

#### Scapy

Un autre outil qui va s'avérer très important dans la suite de ces expérimentations est Scapy. Vous pourrez l'utiliser et le lancer en utilisant les commandes suivantes (à l'intérieur des hôtes netkit, il est possible que cet outil soit déjà installé !) :

```console
git clone https://github.com/secdev/scapy.git
cd scapy
./run_scapy
```

**Q.4** Expliquez ce qu'est Scapy (https://github.com/secdev/scapy) ? Expliquez également quel pourra être l'intérêt de cet outil dans ce contexte.

*Note : Pour pouvoir l'utiliser dans le cadre de ces expérimentations, il faudra le placer dans le dossier /lab/shared. Ainsi, il sera accessibles aux hôtes NetKit. Comme tout fichier/dossier que vous positionnerez dans ce dossier partagé.*

#### Wireshark

Tout au long des expérimentations, nous allons utiliser Wireshark pour analyser les échanges entre les différentes hôtes et en déduire les conséquences des attaques qui seront menées par oscar.

Toutefois, avec NetKit (*vdump* n'étant pas accessible sauf si vous utilisez la VM) l'utilisation de Wireshark au sein des hôtes n'est pas aisée. 

Utilisez https://www.lri.fr/~fmartignon/documenti/reseauxavances/Netkit_enonce_de1a3.pdf (page 4!) pour comprendre comment vous pourrez utiliser Wireshark dans le cadre de cette expérimentation.

*Note : Si vous utilisez la VM, vous pourrez simplement utiliser la commande suivante :* `vdump *nom_ss_reseau* | wireshark -i - -k &`

### 2.B Une première attaque : Man-in-the-Middle

Il s'agit là d'une première attaque réseau envisageable. Les attaques réseau sont une première grande catégorie d'attaques qui peuvent être menées contre les objets IoT.

**Q.5** Ces attaques se décomposent généralement en 4 à 5 grandes étapes, lesquelles ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://blog.f-secure.com/fr/les-5-phases-dune-cyber-attaque-le-point-de-vue-du-pirate/

#### Empoisonnement des tables ARP

**Q.6** Rappelez quelle est l'utilité d'une table ARP et à quoi correspondent les ARP Query and Reply. Indiquez également quel pourrait être l'intérêt de s'attaquer à ces tables ARP (*ARP Spoofing/ARP Poisoning*).

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://fr.wikipedia.org/wiki/ARP_poisoning

L'objectif d'oscar, en utilisant Scapy, va ici être d'empoisonner la table ARP d'alice et de se faire passer pour sa passerelle 10.255.255.254. Ainsi, il recevra le trafic destiné à la passerelle.

Pour ce faire, oscar va devoir envoyer toutes les secondes à alice un paquet ARP forgé indiquant que son adresse MAC correspond à l'adresse MAC de la passerelle.

Pour parvenir à ceci, vous devrez utiliser Scapy qui permet l'envoi et la manipulation de paquets.

Par exemple, l'utilisation de Scapy dans le terminal d'oscar, et le lancement de la commande suivante dans Scapy `sr1(IP(dst="10.0.0.1")/ICMP()/"bonjour")` permettrait d'envoyer un ping à Alice et d'en afficher la réponse.

Pour créer la ligne permettant cette redirection, vous pourrez utiliser différentes commandes de Scapy, notamment (*getmacbyip, Ether, ARP*).

Vous pourrez également vous inspirer de la solution proposée par https://medium.com/datadriveninvestor/arp-cache-poisoning-using-scapy-d6711ecbe112 

*Note :* Attention, contrairement à la solution utilisée ci-dessus, la façon la plus simple de faire sera ici d'utiliser une opération du type `who-has`, oscar se faisant passer pour la passerelle.

**Q.7** Indiquez la ligne de commande que vous avez utilisée pour empoisonner la table ARP d'alice. Indiquez également quels sont les mots clés permettant d'indiquer le nombre de message que l'on souhaite envoyer ainsi que la fréquence.

A l'aide d'un simple `ping` d'alice vers bob, vous pourrez vérifier que l'empoisonnement de la table ARP a bien fonctionné. 

**Q.8** Quelles différences observez vous (avec/sans empoisonnement) ? A quoi correspond cette redirection ?

**Q.9** Quelles contre-mesures peuvent être envisagées pour lutter contre ce genre d'attaques ? 

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://en.wikipedia.org/wiki/ARP_spoofing#Defenses


#### TCP Hijacking

**Q.10** Qu'est ce que ce type d'attaque ? Quel en est le principe ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans  https://www.techrepublic.com/article/tcp-hijacking/

Pour mener simplement et rapidement ce genre d'attaque, nous allons utiliser un outil développé pour cet usage : *shijack*.

Pour pouvoir l'utiliser ici, il vous suffira de télécharger l'archive (https://packetstormsecurity.com/sniffers/shijack.tgz), d'en extraire de contenu et de copier le dossier résultant dans */lab/shared*. Ainsi, cet outil pourra être à disposition des hôtes NetKit.

Pour mener à bien cette attaque, on va mettre en place une connexion telnet entre bob et alice : `telnet 10.0.0.1 23`. L'identifiant/mot de passe à utiliser est *guest/guest*.

Une fois cette connexion établie entre bob et alice, l'objectif d'oscar va être de réussir à "voler cette session" grâce à l'outil *shijack*.

Pour ce faire, il suffira à oscar de lancer une simple ligne de commande.

**Q.11**  Quelle est cette ligne de commande ? A quoi correspondent les différents paramètres ?

Pour répondre à cette question et mener à bien cette expérimentation, vous pourrez vous basez sur la solution proposée dans https://www.tutorialspoint.com/ethical_hacking/ethical_hacking_tcp_ip_hijacking.htm


**Q.12** Quels sont les risques liés à ce genre d'attaques ? Quelles pourraient en être les conséquences ?

#### Man-in-the-Middle avancés

Les attaques de type Man-in-the-Middle sont parmi les principales attaques pouvant être menées, notamment en raison du faible niveau de sécurisation des objets connectés. L'empoisonnement de tables ARP et le vol de session sont quelques exemples de ces attaques. Comme le montre cet article (https://gizmodo.com/why-apples-huge-security-flaw-is-so-scary-1529041062) de nombreuses grandes entreprises, telles qu'Apple, peuvent s'avérer vulnérables à ce type d'attaques.

**Q.13** Quels sont les principaux types d'attaques MITM et les principales techniques utilisées ?

**Q.14** De manière générale, quelles contremesures peuvent être envisagées contre ce type d'attaques ?

Pour répondre à ces deux questions, vous pourrez utiliser les informations présentées dans https://www.rapid7.com/fundamentals/man-in-the-middle-attacks/

### 2.B Une seconde attaque : Déni-de-Service 

**Q.15** Qu'est ce qu'une attaque de Déni-de-Service ? Quel est son objectif ? 

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://fr.wikipedia.org/wiki/Attaque_par_d%C3%A9ni_de_service

#### Smurf

Les attaques de type *Smurf* sont un premier type possible d'attaques de DoS.

**Q.16** Quel est le principe de ces attaques ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://en.wikipedia.org/wiki/Smurf_attack

Créez au niveau d'oscar, une ligne de commande Scapy permettant de forcer alice à envoyer en boucle des requêtes ICMP à l'adresse de broacast 10.255.255.255. Comme indiqué plus tôt, `sr1(IP(dst="10.0.0.1")/ICMP()/"bonjour")` pourrait permettre l'envoi d'une requête ICMP. 

**Q.17** Indiquez la ligne de commande permettant de mener à bien ce genre d'attaque.

**Q.18** Quels sont les risques de ce genre d'attaques ? Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.

#### SYN Flood

Les attaques de type *SYN Flood* sont un second type possible d'attaques de DoS.

**Q.19** Quel est le principe de ces attaques ?

Nous allons maintenant essayer de mener ce genre d'attaque à l'encontre d'alice.

Pour ce faire, il suffit à oscar de créer un paquet multi-couche destiné à alice :

```console
topt=[(‘Timestamp’, (10,0))]
p=IP(dst="...", id=1111,ttl=99)/TCP(sport=RandShort(),dport=[22,80],seq=12345,ack=1000,window=1000,flags=”S”,options=topt)/”SYNFlood”
ans,unans=srloop(p,inter=0.2,retry=2,timeout=6)
```

**Q.20** A quoi servent à votre avis les éléments aléatoires de ce message (id, ttl) ?

Ainsi, avec ces quelques lignes, oscar va envoyer à alice un paquet toutes les 0.2 secondes pendant 6 secondes. Il s'agit là d'un exemple simple correspondant à une attaque de type *SYN Flood*. Cette succession rapide de requêtes SYN pourrait entraîner une surcharge du système d'Alice et pourrait donc rendre impossible l'accès aux services d'alice (par exemple un service telnet).

**Q.21** Quels sont les risques de ce genre d'attaques ? Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.


#### Ping of Death

Les attaques de type *Ping of Death* sont un troisième type possible d'attaques de DoS.

**Q.22** Quel est le principe de ces attaques ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://en.wikipedia.org/wiki/Ping_of_death

Si oscar souhaite mener ce type d'attaque à l'encontre d'alice, avec Scapy, il lui suffira d'utiliser une commande du type `send(fragment(IP(dst=dip)/ICMP()/(‘X’*60000))`.

Lancez cette commande et regarder le wireshark du sous réseau dans lequel est situé alice.

Comme vous pouvez le constater, le paquet ICMP, étant fragmenté, peut être envoyé, bien que sa taille soit supérieure à la taille maximale définie pour ce type de paquet. Toutefois, le réassemblage de ce paquet pourrait perturber le bon fonctionnement d'alice.

**Q.23** Quels sont les risques de ce genre d'attaques ? Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://www.imperva.com/learn/ddos/ping-of-death/

#### Fragment de chevauchement (Overlapping Fragments)

**Q.24** Quel est le principe de ces attaques ? 

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans  https://cyberhoot.com/cybrary/fragment-overlap-attack/

Si oscar souhaitait mener ce genre d'attaque contre alice, il devrait créer trois fragments avec Scapy, en utilisant pour chacun d'eus l'adresse IP d'alice. 

```console
frag1=IP(dst=dstIP, id=12345, proto=1, frag=0, flags=1)/ICMP(type=8, code=0, chksum=0xdce8)
frag2=IP(dst=dstIP, id=12345, proto=1, frag=2, flags=1)/”ABABABAB”
frag3=IP(dst=dstIP, id=12345, proto=1, frag=1, flags=0)/”AAAAAAAABABABABACCCCCCCC”
```

Pour pouvoir analyser les messages reçus par alice, pour cette fois ci, au lieu d'utiliser wireshark, nous pourrons également lancer Scapy chez alice et sniffer les messages qu'elle reçoit grâce à la commande : `a = sniff(filter='icmp')`

Une fois ceci réalisé, oscar peut envoyer séquentiellement les trois fragments à alice :`sent(frag1)...`

On peut alors stopper le sniffer chez alice et analyser les données reçues grâce aux commandes suivantes :

```console
a.nsummary()
a[0]
a[1]
a[2]
a[3]
```
Ceci devrait vous permettre de visualiser un résumé complet des paquets ICMP reçus par alice ainsi qu'un résumé de chacun de ces paquets. Le 4ème paquet montre la réponse d'alice et devrait indiquer que le second fragment contient un décalage incorrect (*offset*).

**Q.25** Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.


#### Attaques de Déni-de-Service avancées

Aujourd'hui, les attaques DoS modernes représentent un danger réel pour les entreprises. En effet, elles pourraient entraîner une perturbation de leur fonctionnement tant en interne qu'en externe. Ces attaques sont capables d'atteindre des débits très élevés de l'ordre de plusieurs millirs de Gb/s. Pour ce faire, ces attaques se basent généralement sur des approches distribuées et sur l'utlisation de réflecteur.

**Q.26** En prenant l'exemple de l'attaque *Mirai*, expliquez ce que sont ces réflecteurs et comment fonctionnent ces attaques DDoS. Indiquez également quelles sont les contre-mesures qui peuvent être mises en place.

Pour répondre à cette question, vous pourrez utiliser les informations présentées dans https://www.imperva.com/blog/how-to-identify-a-mirai-style-ddos-attack/

### 2.C Une troisème attaque : Prise de contrôle

Un autre type d'attaques peut avoir pour objectif de prendre le contrôle d'une machine locale/distance.

#### Prise de contrôle par dépassement de tampon

**Q.27** Qu'est ce qu'une attaque par débordement de mémoire ? Expliquez le principe de façon claire. Quelles peuvent en être les conséquences de ces attaques (ie quel est l'objectif de l'attaquant) ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://aayushmalla56.medium.com/buffer-overflow-attack-dee62f8d6376.

Ainsi, les attaque par dépassement de tampon ont généralement pour objectif de réussir à écrire dans la pile pour écraser l'adresse de retour de la fonction en cours d'exécution et provoquer une redirection vers un autre programme tel qu'un *shellcode* situé dans la pile.

**Q.28** Qu'est ce qu'un *shellcode* ?

Pour mener à bien cette attaque, sur la machine oscar, nous allons utiliser le fichier vulnérable *hello.c*

**Q.29** En analysant la variable buf, indiquez en quoi son utilisation rend le programme vulnérable. Comment pourriez vous provoquer pour ce programme une erreur d'exécution de type "SegFault" ? Que se passerait il alors ?

Le programme *exhello.py* permet de modeler le contenu de la pile du programme *hello*. *exhello.py* prend en argument une adresse utilisée ici pour écraser l'adresse de retour du programme *hello*. Par exemple, l'adresse de la fonction hello pourrait être utilisée :

```console
./exhello.py 0x8048254 | ./hello
```

**Q.30** Que se passe t il lorsque l'on exécute la ligne ci dessus ?

Le programme *exhello.py* permet d'ajouter au début de buf un shellcode. En combinant les commandes *exhello.py*, *hello* et *cat*, faites en sorte qu'il devienne possible d'exécuter une commande shell (n'importe laquelle) à travers le processus hello.

*Note : si vous bloquez sur la définition de cette solution, vous pourrez y revenir en fin de séance.*

**Q.31** Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://www.linuxjournal.com/article/6701

#### Telnetd remote exploit

Pour que la prise de contrôle à distance d'une machine soit possible, différentes conditions doivent être réunies. Tout d'abord le service utilisé pour cette prise de contrôle doit disposé de failles exploitables. Ensuite, ce service doit disposer de privilèges élevés. *ssh* et *telnet* sont des exemples intéressants de ce type de services (failles + privilèges).

Pour ces services, différentes failles ont déjà été rendues publiques. Pour telnet, c'est par exemple le cas de la faille CVE-2011-4862. Ainsi, des exploits permettant d'exploiter cette faille on été publiés à l'image de *telnetd-encrypt_keyid*. Cette faille étant rendu publique, elle n'est bien entendu plus exploitable sur la plupart des systèmes actuels (mises à jour). Toutefois, dans le cadre de cette expérimentation, la machine alice a été configurée pour être vulnérable à ce type d'attaques.

Le code permettant de l'exploiter a été déployé sur oscar et adaptée à ce TP.

Ainsi, sans même connaître le mot de passe du super-utilisateur d'alice, grâce à cette exploit, vous pourrez constater qu'il est possible d'obtenir pour oscar un shell root sur la machine alice : 

```console
./paf ip_alice 23 3
```

Utilisez Wireshark pour analyser les messages échangés.

**Q.32** En vous basant sur ces observations, ainsi que la documentation de la faille CVE-2011-4862 (https://nvd.nist.gov/vuln/detail/CVE-2011-4862) et le code du programme *paf.c*, expliquez comment fonctionne cette attaque. 

**Q.33** Indiquez les contre-mesures qui peuvent être proposées contre ce genre d'attaques.


## 3. Etude théorique : Attaques par canaux auxilaires (*Side-channel attack*)

Dans cette partie, au travers d'une étude théorique, nous allons nous intéresser aux attaques pouvant être menées au niveau de l'objet IoT lui même (matérielles). Plus particulièrement, nous allons nous intéresser aux attaques par canaux auxiliaires. 

**Q.34** Qu'est ce qu'une attaque par canaux auxiliaires ? Que cherche-t-on généralement à deviner ? Quelles valeurs peuvent être mesurées ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : http://www.bibmath.net/crypto/index.php?action=affiche&quoi=chasseur/canalauxiliaire

### 3.A Attaque simple par analyse de courant (*Simple Power Analysis*)

Une première attaque par canaux auxiliaires possible, parmi les plus simples, est l'attaque nommée *Simple Power Analysis (SPA)*.

Cette attaque consiste à mesurer directement la consommation d'un circuit électrique. L'objectif de cette attaque (ainsi que des autres attaques présentées dans cette partie **3**) est de deviner la clé privée utilisée par un objet IoT.

Pour comprendre le fonctionnement de cette attaque, nous allons nous focaliser ici sur l'algorithme à clé publique RSA (Rivest–Shamir–Adleman), couramment utilisé dans l'environnement IoT.

Avec cet algorithme, la fonction utilisée pour le déchiffrement d'un message est la suivante :

**M=C<sup>d</sup> mod(N)**

où *M* correspond au message déchiffré, *C* au message chiffré, *d* à la clé privée de l'objet IoT et *N* à la multiplication de deux entiers premiers.

Ainsi, l'objectif d'un attaquant serait ici de déterminer la valeur de *d*.

Or, avec RSA, la méthode la plus efficace pour calculer l'exponentiation d'un entier par un autre est la méthode dite d'exponentiation rapide :

_____
**Objectif, calculer d :**

1) Calculer la décomposition binaire de d

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

**Q.35** Si vous étiez un attaquant, en considérant l'algorithme ci-dessus, comment pourriez vous être en capacité de deviner la clé de l'objet IoT ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées aux slides 25 et 26 dans https://www.emse.fr/~nadia.el-mrabet/Presentation/Cours5_SCA.pdf

**Q.36** Pour éviter ce problème et offrir une solution "à consommation constante", que le bit traité soit un 0 ou un 1, quelle contremesure pourriez vous proposer ?

### 3.B Attaque par injection de fautes 

Une contremesure que vous pourriez avoir introduit dans la partie précédente pourrait consister à introduire une opération factice lorsque le bit est à 0 :

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

**Q.37** Quelle contremesure pourrait être proposée pour résoudre ce problème ?

Pour répondre à cette question, vous pourrez utiliser les informations présentées au slide 37 dans https://www.emse.fr/~nadia.el-mrabet/Presentation/Cours5_SCA.pdf


### 3.C Ouverture : Attaque différentielle par analyse de courant (*Differential Power Analysis*)

Alors que les attaques SPA et par injection de fautes font partie des attaques "simples", pouvant être exécutées dans un environnement peu protégé (ce qui correspond à bon nombre d'objets IoT), les attaques DPA font partie d'attaques plus complexes pouvait être exécutées contre des systèmes mieux protégés.

L'attaque DPA part de l'idée que la consommation de puissance est différente selon les états (bit à 0 ou 1) et les transitions (1->0 ou 0->1).

Toutefois, ces variations sont infinitésimales et peuvent être cachées par l'existence de bruit ou une erreur de mesure.

Par conséquent, l'approche DPA repose également sur une analyse statistique : l'attaquant va répéter plusieurs fois (1000, 10000) une même opération afin d'en déduire des tendances.

Ainsi, grâce à cette approche, l'attaquant pourra être en mesure (après un certain temps...) d'associer la mesure d'un instant donné à un état (ou une transition) et par conséquent d'en déduire la clé privée de l'objet IoT. 

Cette attaque, basée sur des variations très petites, et non liée à une erreur dans l'implémentation de l'algorithme de chiffrement, est bien plus puissante que les attaques SPA et d'injections de fautes. Toutefois, des contremesures sont possibles.

**Q.38** Quelles contremesures pourraient être proposées contre ce genre d'attaques ?

## 4. Pour aller plus loin

Au delà des différentes expérimentations proposées dans les sections précédentes, vous pourriez par vous même mettre en pratique d'autres types d'attaques. Pour ce faire le projet SEED pourrait être un outil intéressant offrant de nombreuses ressources.

**Q.39** Indiquez, au delà des attaques réseau et logicielles, les types d'attaques auxquels vous pourriez vous former grâce à ce projet.

Pour répondre à cette question, vous pourrez utiliser les informations présentées par : https://seedsecuritylabs.org/Labs_20.04/ 

Au delà des contremesures actuellement implémentées, de nombreux organismes travaillent à la définition de nouvelles solutions qui pourraient permettre de renforcer la sécurité des objets connectés. Parmi les solutions potentielles, une technologie que nous avons étudié dans le cadre d'un autre TP est fortement mise en avant : la Blockchain.

**Q.40** Indiquez pourquoi la Blockchain, grâce à ses caractéristiques, pourrait sembler être une solution pertinente pour la sécurisation des objets connectés. Donnez également des exemples de solutions existantes basées sur la Blockchain.

Pour répondre à cette question vous pourrez utiliser les informations présentées par : https://www.itransition.com/blog/blockchain-iot-security 

Pour votre culture personnelle (ou votre divertissement ?) un outil, évoqué dans l'introduction de ce TP, pourrait vous être utile : Shodan.

**Q.41** Qu'est ce que Shodan (https://www.shodan.io/) ? Comment cet outil semble-t-il fonctionner ? Comment pourrait-il être utilisé ? Quels sont les risques ?

D'autres outils qui pourraient vous intéresser : matasploit, Kali Lunix

