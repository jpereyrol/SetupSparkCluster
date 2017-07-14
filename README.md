# Installation et utilisation d'un cluster Spark

L'objectif de ce tutoriel est de vous apprendre à installer un cluster Spark avec les paramètres minimaux requis pour son fonctionnement. Vous pourrez l'utiliser pour des traitements dans le **spark-shell** ou encore pour des applications spark via **spark-submit**. Si vous souhaitez creuser d'avantage ce sujet je vous invite à améliorer la configuration ici proposé pour combler vos besoins et désirs.

**Table des matiéres**

* [Introduction](https://github.com/jpereyrol/SetupSparkCluster#1-introduction)
* [SSH entre esclaves et maître](https://github.com/jpereyrol/SetupSparkCluster#2-ssh-entre-esclaves-et-maître)
* [Configuration des esclaves](https://github.com/jpereyrol/SetupSparkCluster#3-configuration-des-esclaves)
* [Configuration du maître](https://github.com/jpereyrol/SetupSparkCluster#4-configuration-du-maître)
* [Utilisation du cluster](https://github.com/jpereyrol/SetupSparkCluster#5-utilisation-du-cluster)

## 1. Introduction

Le terme de cluster (grappe en français) fait référence à une batterie d'ordinateurs, tous interconnéctés, dans le but de partager des ressources informatiques.

[IMAGE FONCTIONNEMENT CLUSTER]

**TODO : Explication fonctionnement cluster spark**

[IMAGE FONCTIONNEMENT CLUSTER SPARK]

Pour réaliser ce cluster, nous allons donc avoir besoin de :

  * Un maître
  * Un ou des esclaves
  * Un même sous-réseau pour toutes les machines
  * Une connexion internet fonctionnelle
  
En plus de devoir être dans le même sous-réseau il faut qu'elles aient toutes des IP différentes. Je précise cela car c'est un probléme récurrent lors des tests avec des machines virtuelles par exemple.
  
Beaucoup de choix s'offrent à vous pour ces "nodes" (maître, esclaves), en voici quelques uns pour vous aider :
  
  * Plusieurs ordinateurs (raspberry pi, vieux ordinateurs)
  * Amazon AWS
  * Installation d'une plateforme virtuelle avec plusieurs machines virtuelles pour simuler ce cluster
 
Pour le choix de l'OS, nous allons utiliser Ubuntu 16.04 LTS pour sa stabilité et simplicité d'installation. Néanmoins j'assure qu'il est possible de faire la même chose sur Windows et MacOS, avec un peu plus de difficultés.

**Conseil : créez un utilisateur 'spark' sur toutes les machines, cela vous évitera beaucoup de problèmes de permissions.**

Maintenant, passons à l'installation !

## 2. SSH entre esclaves et maître

Afin de pouvoir administrer toutes les machines depuis la votre, nous allons échanger les clés SSH entre chaques nodes.

Pour chaque slave il faut faire l'action suivante :

**Sur le maître :**

Générer une clé public : 

```bash
ssh-keygen -t rsa
```

Il faut ensuite ajouter cette clé public dans le fichier authorized_keys du slave, on peut le faire directement depuis le maître :

```bash
ssh-copy-id user@<ip-address or hostname>
```
*Il faudra entrer le mot de passe de la session slave pour se connecter*

**Sur l'esclave :**

Générer une clé public :

```bash
ssh-keygen -t rsa
```

Il faut ensuite ajouter cette clé public dans le fichier authorized_keys du maître, on peut le faire directement depuis l'esclave avec :

```bash
ssh-copy-id user@<ip-address or hostname>
```
*Il faudra entrer le mot de passe de la session maître pour se connecter*

Vous voilà maintenant prêt pour configurer facilement le maître et tout les esclaves !

## 3. Configuration des esclaves

**ATTENTION** : Pour toutes ces installations, il faut que le chemin soit identique sur toutes les machines.

#### A. Installation de Java

Veuillez suivre ce tutoriel :

[Installer Java 8 sur Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04)

#### B. Installation de Scala

Scala est diponible via *apt* directement :

```bash
sudo apt-get install scala
```

#### C. Installation de Spark

Vous pouvez télécharger spark [ici](http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz), ou bien utilisez cette commande pour la version 2.1.0 (28 Décembre 2016) :

```bash
wget http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz
```

Il faut décompresser ce tgz pour ensuite placer cette librairie quelque part (/usr/share car c'est aussi là que scala se trouve) :

```bash
tar -xvzf spark-2.1.0-bin-hadoop2.7.tgz
sudo mv spark-2.1.0-bin-hadoop2.7 /usr/share/
```

#### D. Ajout des variables d'environnements

Une bonne méthode pour modifier des variables d'environement et s'assurer qu'elle sont initialisées à chaque démarrage est de les ajouter dans le *~/.bashrc*.

Pour ouvrir votre fichier de configuration :

```bash
nano ~/.bashrc
```

Ensuite à la fin de votre fichier ajoutez : 

```bash
export SCALA_HOME=/usr/share/scala-<version>
export SPARK_HOME=/usr/share/spark-<version>-bin-hadoop<version>
export PATH=$PATH:$SPARK_HOME/bin
```

Pour rendre les changements effectifs :

```bash
source ~/.bashrc
```

## 4. Configuration du maître

La configuration est presque identique, mais attention aux petites erreurs toujours. 

#### A. Installation de Java

Veuillez suivre ce tutoriel :

[Installer Java 8 sur Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-get-on-ubuntu-16-04)

#### B. Installation de Scala

Scala est diponible via *apt* directement :

```bash
sudo apt-get install scala
```

#### C. Installation de Spark

[ici](http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz)

Ou directement dans le terminal :

```bash
wget http://d3kbcqa49mib13.cloudfront.net/spark-2.1.0-bin-hadoop2.7.tgz
```

Il faut decompresser ce tgz pour ensuite placer cette librairie quelque part :

```bash
tar -xvzf spark-2.1.0-bin-hadoop2.7.tgz
sudo mv spark-2.1.0-bin-hadoop2.7 /usr/share/
```

#### D. Ajout des variables d'environnements

La meilleure méthode pour modifier des variables d'environement est de les ajouter dans le *~/.bashrc* (ou zsh, fish ...)
Pour ouvrir votre fichier de configuration :

```bash
nano ~/.bashrc
```

Ensuite à la fin de votre fichier ajoutez : 

```bash
export SCALA_HOME=/usr/share/scala-2.11
export SPARK_HOME=/usr/share/spark-2.1.0-bin-hadoop2.7
export PATH=$PATH:$SPARK_HOME/bin
```

Pour rendre les changements effectifs :

```bash
source ~/.bashrc
```

#### E. Configuation de /etc/hosts

On ajoute dans ce fichier l'addresse IP de chaque esclave, restez sobre, il faut s'en souvenir :

```
<ip-address-slave-1>          slave-1
<ip-address-slave-2>          slave-2
<ip-address-slave-3>          slave-3
```

#### F. Configuration des scripts pour lancer les slaves

Nous allons maintenant faire la configuration de spark. Il y a deux fichiers à modifier, *slaves* et *spark-env.sh* :

```bash
cd $SPARK_HOME/conf/
cp slaves.template slaves
cp spark-env.sh.template spark-env.sh
```

Dans le fichier *slaves*, il faut indiquer tout les esclaves de notre cluster. Si vous avez utilisé /etc/host vous pouvez mettre directement le *hostname* sinon l'adresse IP.

```
slave-1
slave-2
slave-3
```

Le fichier *spark-env.sh* nous permet de configurer le cluster et son comportement. Je vais juste indiquer les paramètres minimaux pour le fonctionnement du cluster mais je vous invite à vous réferer à la documentation si vous souhaitez en ajouter.

```
export SPARK_MASTER_HOST=<master-ip-address>
export SPARK_LOCAL_IP=<current-node-ip-address>
```

## 5. Lancement du cluster

#### A. Lancement du maître

Sur la machine maître :

```bash
/usr/share/spark-2.1.0-bin-hadoop2.7/sbin/start-master.sh
```

#### B. Lancement des esclaves

Sur chaque esclave :

```bash
/usr/share/spark-2.1.0-bin-hadoop2.7/sbin/start-slave.sh spark://<master-ip>:7077
```

Vous pouvez vérifier que tout fonctionne sur la Web UI du cluster qui se trouve à l'URL :

spark://<master-ip>:8080

## 6. Utilisation du cluster

#### A. Spark-shell

Commande à lancer sur la machine maître :

```
bin/spark-shell --master spark://<master-ip>:7077
```

Pour exécuter du code depuis le spark-shell, il ne faut pas de *main* (ou de *extends App*), et il ne faut pas oublier les import !

#### B. Fat JAR (SBT)

Pour la création d'un Fat JAR je vous invite à suivre ce tutoriel : [Création d'un Fat JAR](https://stackoverflow.com/questions/28459333/how-to-build-an-uber-jar-fat-jar-using-sbt-within-intellij-idea)

Une fois le fat JAR créer il vous suffit de le submit au cluster via la commande :

```bash
/usr/share/spark-2.1.0-bin-hadoop2.7/bin/spark-submit --master spark://<master-ip>:7077 --class <main-class-name> ~/Path/To/Jar/<jar-name>.jar
```
**IMPORTANT : vous pouvez submit depuis n'importe quelle machine tant que vous avez spark sur celle-ci**

## 7. Conclusion

Vous savez maintenant installer et utiliser un cluster standalone spark ... **TODO**

_________________________________________________________________________________________________________________________________

#### Sources : 

- Spark GraphX in Action - *Michael S. Malak* and *Robin East*
- [Qu'est ce qu'un Cluster](http://www-igm.univ-mlv.fr/~dr/XPOSE2001/vayssade/qu-est%20ce%20qu-un%20cluster.htm) - *Renaud Vayssade*
