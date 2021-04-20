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

 - copier la clé publique vers la machine **cloud ** (IP_Cloud dans mon cas : 192.168.1.108)

  ```shell
  $ cat ~/.ssh/id_rsa.pub | ssh mpuser@192.168.1.108 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
  ```
![copy key](https://user-images.githubusercontent.com/54450458/115446987-40805880-a218-11eb-9ca6-2eddc963a1b0.PNG)

 - **N.B** vous pouvez utilser les noms de les machine au lieu de ces adresses à condition les rensignés déja dans le fichie `/etc/hosts` de chaque machine
- maintenant on peut accéder au machine cloud sans mot de passe , taper `exit`pour retournerez 
   ![acces](https://user-images.githubusercontent.com/54450458/115447490-dc11c900-a218-11eb-92f7-47529874b3be.PNG)




- Sur la machine **cloud** générer les clés et les copier vers cloud (IP_Client dans mon cas : 192.168.1.109 :

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

