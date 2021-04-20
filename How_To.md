# Mise en Place d'une Cluster MPI

Dans notre cas on vas utiliser deux machines virtuels  sous VMware Workstation 



### Étape 1 : Configuration des Machines 

1. **Changer le nom des machines**  :

   Pour changer le nom d'une machines on modifier le nom de la machine dans les fichiers `/etc/hostname` (le nom de la machine local ) `/etc/hosts` (la machine local et distant) on va nommer une Machine **cloud** et l'autre **client** .

   La commande utilisée pour modifier  :

    ``` shell
    $ sudo nano /etc/hostname
    $ sudo nano/etc/hosts
    ```

   Pour la vérification après le redémarrage :

   ``` shell
    $ hostname
   ```
 2. **Vérification de la connectivité entre les deux Machine :**

   - vérification des adresses IP :soit avec la commande `# ifconfig` ou `# ip a`

   - utilisation de ping 

   - vérification des paramètres de "network adapter " dans les "setting" de VMWare les deux machine doit avoir les même "network Connection" types .
 
 
![0](https://user-images.githubusercontent.com/54450458/115442808-bf729280-a212-11eb-8bd9-a54865f51581.png)


 3. **Création d'utilisateur :**

      dans les deux machines on va créer un utilisateur avec le même identifiant  avec la commande suivante en assignant un mot de passe après la commande :

      sur  **cloud** :

      ``` shell
      $ sudo adduser mpuser
      $ sudo usermod mpuser -G sudo
      ```
      sur **client** :

     ``` shell
      $ sudo adduser mpuser
      $ sudo usermod mpuser -G sudo
     ```
    * Se connecter avec le compte **mpuser** sur les deux machines **cloud** et **client** avec la commande suivante : 
    ``` 
    $ su mpuser
    ```
 
4. **Installation de ssh :**

      - La communication entre les deux machines sera établie via ssh alors pour installer ssh 

        on va suivre les commandes suivantes sur les deux machines **cloud** et **client** :

      ```shell
      $ sudo apt update
      $ sudo apt install openssh-server
      ```

      et taper votre mot de passe et y pour accepter 

      - vérifier l'état de service ssh par la commande suivante :

      ```shell
      $ sudo service ssh status
      ```

      ​	où 

      ```shell
      $ sudo systemctl status ssh
      ```

      - si le firewall ubuntu est activé autoriser le ssh par la commande suivante 

      ```shell
      $ sudo ufw allow ssh
      ```
5. **configuration de ssh sécurité :** configurer la connexion SSH sans mot de passe

- Sur la machine **client** générer les clés RSA 

  ```shell
  $ ssh-keygen -t rsa
  ```
  - tapez entrée pour laisser les paramètres par défaut
 ![generate Key](https://user-images.githubusercontent.com/54450458/115446977-3cecd180-a218-11eb-81c6-76b3dcb951e0.PNG)

 - copier la clé publique vers la machine **cloud** (IP_Cloud dans mon cas : 192.168.1.108)

  ```shell
  $ cat ~/.ssh/id_rsa.pub | ssh mpuser@192.168.1.108 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
  ```
![copy key](https://user-images.githubusercontent.com/54450458/115446987-40805880-a218-11eb-9ca6-2eddc963a1b0.PNG)

 - **N.B** vous pouvez utilser les noms de les machine au lieu de ces adresses à condition les rensignés déja dans le fichie `/etc/hosts` de chaque machine
- maintenant on peut accéder au machine cloud sans mot de passe , taper `exit`pour retournerez 
   ![acces](https://user-images.githubusercontent.com/54450458/115447490-dc11c900-a218-11eb-92f7-47529874b3be.PNG)




- Sur la machine **cloud** générer les clés et les copier vers client (IP_Client dans mon cas : 192.168.1.109 :

  répéter presque les même étapes précédentes 

  ```shell
  $ ssh-keygen -t rsa
  $ cat ~/.ssh/id_rsa.pub | ssh mpuser@192.168.1.109 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
  ```
### Étape 2 : Installation de NFS
#### sur la Machine cloud
1. Installer NFS server
  ```shell
  $ sudo apt-get install nfs-kernel-server
  ```
2. créer un répertoire de Travail qui sera partager à travers le réseau et contiendra les script MPI 
    ```shell
   $ mkdir mpicloud
    ```
3. Exporter le répertoire mpicloud en ajoutant cette ligne dans le fichier `/etc/exports`

```$ sudo nano /etc/exports ```

   Et ajouter la ligne suivante `/home/mpuser/mpicloud *(rw,sync,no_root_squash,no_subtree_check)`

- verfication de la tache :

![image](https://user-images.githubusercontent.com/54450458/115458798-7e847900-a226-11eb-929c-06e0cf7721d3.png)

4. appliquer  la dernière modification 
```
 $ exportfs -a
```
5. Redémarrer le service NFS :
```
$ sudo service nfs-kernel-server restart
```
#### sur la Machine client
1. installer le package nfs-common

```
$ sudo apt-get install nfs-common
```
2. créer un répertoire de Travail qui sera partager à travers le réseau et contiendra les script MPI 
    ```shell
   $ mkdir mpicloud
   ```
3. Creation du point de montage
```
sudo mount -t nfs cloud:/home/mpuser/mpicloud ~/mpicloud
```
4. Vérification : 
```
$ df -h 
```
![image](https://user-images.githubusercontent.com/54450458/115460348-74fc1080-a228-11eb-8fad-340c9a1345a5.png)

5. Rendre le montage permanant :
dans le fichier `/etc/fstab ` on ajoute la ligne suivante : 

        cloud:/home/mpuser/mpicloud /home/mpuser/mpicloud nfs 

 - Vérification 
 
 ![image](https://user-images.githubusercontent.com/54450458/115460940-28fd9b80-a229-11eb-86a5-6fc4c9f7ac5f.png) 
 - le point de montage s'affiche dans votre desktop 

![image](https://user-images.githubusercontent.com/54450458/115461127-5e09ee00-a229-11eb-9b82-72ddfab7ca26.png)

### Etape 3 : Test et Exécution :
- si on a plusier client on va répeter juste la configuration sur la machine client pour les autres machine 
### Sur la Machine Cloud 
1. Acceder au repertoire mpicloud
```
cd mpicloud
```
2. créer le fichier cpi.c 
```
nano cpi.c
```
3. coller le code suivant :

```#include "mpi.h"
#include <stdio.h>
#include <math.h>

double f(double);

double f(double a)
{
    return (4.0 / (1.0 + a * a));
}

int main(int argc, char *argv[])
{
    int n, myid, numprocs, i;
    double PI25DT = 3.141592653589793238462643;
    double mypi, pi, h, sum, x;
    double startwtime = 0.0, endwtime;
    int namelen;
    char processor_name[MPI_MAX_PROCESSOR_NAME];
    MPI_Init(&argc, &argv);
    MPI_Comm_size(MPI_COMM_WORLD, &numprocs);
    MPI_Comm_rank(MPI_COMM_WORLD, &myid);
    MPI_Get_processor_name(processor_name, &namelen);

    fprintf(stdout, "Process %d of %d is on %s\n", myid, numprocs, processor_name);
    fflush(stdout);

    n = 10000;  /* default # of rectangles */
    if (myid == 0)
        startwtime = MPI_Wtime();

    MPI_Bcast(&n, 1, MPI_INT, 0, MPI_COMM_WORLD);

    h = 1.0 / (double) n;
    sum = 0.0;
    /* A slightly better approach starts from large i and works back */
    for (i = myid + 1; i <= n; i += numprocs) {
        x = h * ((double) i - 0.5);
        sum += f(x);
    }
    mypi = h * sum;

    MPI_Reduce(&mypi, &pi, 1, MPI_DOUBLE, MPI_SUM, 0, MPI_COMM_WORLD);

    if (myid == 0) {
        endwtime = MPI_Wtime();
        printf("pi is approximately %.16f, Error is %.16f\n", pi, fabs(pi - PI25DT));
        printf("wall clock time = %f\n", endwtime - startwtime);
        fflush(stdout);
    }

    MPI_Finalize();
    return 0;
}
```
4. Compiler et exécuter 
```
$ mpicc cpi.c -o cpi.sh
$ mpirun -n 2 -H client,cloud cpi.sh
``` 
![image](https://user-images.githubusercontent.com/54450458/115465107-5dc02180-a22e-11eb-8156-c71146574733.png)

### Pour utilser Pyhton 
vous devez installer d'abord les paquets suivants sur les deux machines cloud et client  :
```
$ sudo apt install python3-pip
$ pip3 install mpi4py
$ pip3 install MPI
```
 - Exemple de code d'un programme test.py 
 ```
 from mpi4py import MPI

comm=MPI.COMM_WORLD
rank = comm.rank
size = comm.size
name = MPI.Get_processor_name()
print(" I'm Proccess with ID : {} executed From Computer : {} Total Process : {}  ".format(rank,name,size))

 ```
- pour excuter le code : 
``` 
   mpirun -np 2 -H cloud,client python3 -m mpi4py test.py

```
- on peut aussi utiliser un fcihier qui contiendra les adreeses IP de nos machine 
 ```
 mpirun -np 2 -hostfile machines python3 -m mpi4py pfe.py
 ```

![image](https://user-images.githubusercontent.com/54450458/115467445-cfe63580-a231-11eb-84ec-6a43579ba5eb.png)
