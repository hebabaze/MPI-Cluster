# Mise en Place d'une Cluster MPI

Dans notre cas on vas utiliser deux machines virtuels  sous VMware Workstation 



### Étape 1 : Configuration des Machines 

1. **Changer le nom des machines**  :

   Pour changer le nom d'une machines on modifier le nom de la machine dans le fichier `/etc/hostname` on va nommer une Machine **cloud** et l'autre **client** .

   La commande utilisée pour modifier  :

    ``` shell
    $ sudo nano /etc/hostname 
    ```

   Pour la vérification après le redémarrage :

   ``` shell
   $ hostname
   ```

   2 **Vérification de la connectivité entre les deux Machine :**

   - vérification des adresses IP :soit avec la commande `# ifconfig` ou `# ip a`

   - utilisation de ping 

   - vérification des paramètres de "network adapter " dans les "setting" de VMWare les deux machine doit avoir les même "network Connection" types .

     ----------- img 0

   3. **Création d'utilisateur :**

      dans les deux machines on va créer un utilisateur avec le même identifiant  avec la commande suivante en assignant un mot de passe après la commande :

      sur  **cloud** :
   
      ``` shell
      $ sudo adduser mpuser
      ```
      
      
      sur **client** :
     ``` shell
      $ sudo adduser mpuser
     ```
 
   4. **Installation de ssh :**

      - La communication entre les deux machines sera établie via ssh alors pour installer ssh on va suivre les commandes suivantes sur les deux machines **cloud** et **client** :
   
      ```shell
      $ sudo apt update
      $ sudo apt install openssh-server
  ```
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
    
      si le firewall ubuntu est activé autoriser le ssh par la commande suivante 
    
      ```shell
      $ sudo ufw allow ssh
      ```


​      

   5. 

      

