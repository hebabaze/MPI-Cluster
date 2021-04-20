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
5. **configuration de ssh sécurité :**

- Sur la machine **cloud** générer les clés et les copier vers client  (IP_Client dans mon cas : 192.168.1.106)

  ```shell
  $ ssh-keygen -t dsa
  ```

  - tapez entrée pour laisser les paramètres par défaut

 ![ssh0](https://user-images.githubusercontent.com/54450458/115442636-876b4f80-a212-11eb-859f-1391c166d9e4.png)


  ```shell
  $ ssh-copy-id 192.168.1.106
  ```

  ![ssh1](https://user-images.githubusercontent.com/54450458/115442659-8d613080-a212-11eb-963d-87211d4f9b61.png)


 ```shell
 $ eval  ssh-agent
 ```

  ```shell
  $ ssh-add ~/.ssh/id_dsa
  ```

![ssh3](https://user-images.githubusercontent.com/54450458/115442696-981bc580-a212-11eb-82d0-d7b4b7bd6d17.PNG)


- Sur la machine **client** générer les clés et les copier vers cloud (IP_Cloud dans mon cas : 192.168.1.107 :

  répéter presque les même étapes précédentes 

  ```shell
  $ ssh-keygen -t dsa
  $ ssh-copy-id 192.168.1.107
  $ eval `ssh-agent`
  $ ssh-add ~/.ssh/id_dsa
  ```

  
