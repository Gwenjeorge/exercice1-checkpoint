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


![2](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/2LSBLK1.png)

Formater le disque avec le système de fichier ext4 :  
```mkfs.ext4 /dev/sdb1```   

![3](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/3mfks.png)

Changer le label de sb1 par DATA :
tune2fs /dev/sb1 -L DATA


![4](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/4data.png)

Créer ensuite un dossier DATA dans /mnt : ```mkdir /mnt/DATA ```  
Choisir comme point de montage le /DATA précédemment créé :    
``` mount /dev/sdb1 /mnt/DATA```  

Entrez  ``` df -h``` pour véritifier qu'une partition sdb1 a bien été monté sur /mnt/DATA  


![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/6MOUNT2.png)

### Partition SWAP

Pour la partition SWAP, nous allons utiliser l'autre méthode : fdisk  
`n` pour créer une nouvelle partition  
 `p` pour une partition primaire.  
Utiliser ensuite le numéro par défault (ici2) et l'espace par default (fdisk va automatiquement utiliser tout l'espace restant disponible, 4G)  
`t` puis `82` pour choisir le type swap  
`w` pour écrire les modifications  

![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/10FDISK.png)
![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/11FDISK2.png)

``` swapoff /dev/sda5```  pour retirer le swap original  
``` swapon /dev/sdb2``` pour basculer le swap sur la nouvelle partition  

![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/12swap.png)

modifier le label du disque swap (swapoff avant la commande si comme moi vous avez oublié de le faire plus tôt)  

![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/15SWAP.png)


### Montage automatique au demarrage
Récupérer l'UUID du disk DATA avec ```lsblk -o NAME,UUID```  

![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/8UUID.png)

Activer le montage automatique au redémarrage en modifier le fichier de configuration fstab :``` nano /etc/fstab```
`UUID=<UUID_du_disque> /mnt/DATA ext4 defaults 0 2`  
On retrouve l'UUID, le point de montage, le type, et les configuration par défaults  

![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/9fstab.png)

Ensuite redémarrer la VM : ``` reboot```  

``` lsblk```pour vérifier que le disque sdb1 est toujours monté

![1](https://github.com/Gwenjeorge/exercice1-checkpoint/blob/main/Ex1_Screenshots/21reboot.png)

On peut  voir en revanche que le swap a rebasculé au redémarrage sur le disque sda5, le fichier fstab n'ayant été modifié que pour le disque DATA (sdb1)
