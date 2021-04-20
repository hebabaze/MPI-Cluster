# Mise en Place d'une Cluster MPI

Dans notre cas on vas utiliser deux machines virtuels  sous VMware Workstation 



### Étape 1 : Configuration des Machines 

1. **Changer le nom des machines**  :

   Pour changer le nom d'une machines on modifier le nom de la machine dans le fichier `/etc/hostname` on va nommer une Machine **cloud** et l'autre **client** .

   La commande utilisée pour modifier  :

   `#sudo nano /etc/hostname`

   Pour la vérification après le redémarrage :

   `#hostname` 

   2 vérification de la connectivité entre les deux Machine :

   - vérification des adresses IP :soit avec la commande `#ifconfig` ou `#ip a`
   - utilisation de ping 
   - vérification des paramètres de "network adapter " dans les "setting" de VMWare les deux machine doit avoir les même "network Connection" types .

.

