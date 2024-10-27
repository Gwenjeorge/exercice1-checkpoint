### Partition DATA 
``` lsblk```  : Affiche les disques avec entre autre un dique sda monté sur / et un disque sdb de 10G non monté que nous allons utiliser.  

``` cfdisk /dev/sdb```    
Affiche une console pour créer des partitions sur le disque sdb

Nous sélectionnons ensuite le type de partitionnement "gpt"   
Puis "Nouvelle partition"  
Tapez 6G  
Choisir "Ecrire", valider et quitter cfdisk  

![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/1CFDISK.png)

Refaire ``` lsblk``` pour véritifier qu'une partition sdb1 a bien été créé  

Formater le disque avec le système de fichier ext4 :  
mkfs.ext4 /dev/sdb1  

Créer ensuite un dossier DATA dans /mnt : ```mkdir /mnt/DATA ```  
Choisir comme point de montage le /DATA précédemment créé :    
``` mount /dev/sdb1 /mnt/DATA```  

Refaire  ``` lsblk``` pour véritifier qu'une partition sdb1 a bien été monté sur /mnt/DATA  

### Partition SWAP

Pour la partition SWAP, nous allons utiliser l'autre méthode : fdisk  
`n` pour créer une nouvelle partition  
 `p` pour une partition primaire.  
Utiliser ensuite le numéro par défault (ici2) et l'espace par default (fisk va automatiquement utiliser tout l'espace restant disponible, 4G)  
`t` puis `82` pour choisir le type swap  
`w` pour écrire les modifications  

``` swapoff /dev/sda5```  pour retirer le swap original  
``` swapon /dev/sdb2``` pour basculer le swap sur la nouvelle partition  

``` lsblk``` pour confirmer le basculement.  

![swapon.png](https://github.com/Gwenjeorge/screen_capture/blob/5664feecfdb4acdf5415a1054811160a563de061/swapon.png)

### Montage automatique au demarrage
Activer le montage automatique au redémarrage en modifier le fichier de configuration fstab :``` nano /etc/fstab```
`UUID= /mnt/DATA ext4 defaults 0 2`  
On retrouve l'UUID, le point de montage, le type, et les configuration par défaults  

Ensuite redémarrer la VM : ``` reboot```  

``` lsblk```pour vérifier que le disque sdb1 est toujours monté  
On peut  voir en revanche que le swap a rebasculé au redémarrage sur le disque sda5, le fichier fcstab n'ayant été modifié uniquement concernant le disque DATA.  
