# Compte Rendu TP 6 - Gestion des disques, Boot, Gestion des logs
Maxime Dumas & Raphaël Sabran

## Exercice 1. Disques et partitions

### 2. Vérifiez que ce nouveau disque dur est bien détecté par le système :
> On fait la commande ``lsblk -l`` et on voit qu'apparait **sdb** (nouveau disque) en plus de sda (disque système d'origine).

### 3. Partitionnez ce disque en utilisant fdisk : créez une première partition de 2 Go de type Linux (n°83), et une seconde partition de 3 Go en NTFS (n°7) :
>  Pour partitionner le disque **sdb** on utilise la commande ``sudo fdisk /dev/sdb``. On tape **n** pour créer une nouvelle partition puis pour écrit +2G dans "last sector" pour faire une première partition de 2Go de format Linux. On fait de même pour la deuxième partition mais, comme par défaut ``fdisk`` ne créé pas une partition NTFS, il faut changer son type. Dans ``fdisk``, grâce à la fonction **t**, on peut choisir quelle partition modifier et choisir le type : on tape donc **7** pour avoir une deuxième partition NTFS.
>  Pour que la table de partitions soit modifiée il ne faut pas oublier d'utiliser la commande **w** de ``fdisk`` à chaque fois qu'on créé une nouvelle partition ou qu'on la modifie.
>  
> On observe bien nous 2 partitions ci-dessous. 

![1](https://user-images.githubusercontent.com/62430323/77758934-2172bf80-7034-11ea-9edc-38891323907a.png)

### 4. A ce stade, les partitions ont été créées, mais elles n’ont pas été formatées avec leur système de fichiers. A l’aide de la commande mkfs, formatez vos deux partitions :
> Pour la partition Linux on utilise la commande  ``sudo mkfs -t ext4 /dev/sdb1`` et pour la NTFS ``sudo mkfs -t ntfs /dev/sdb2``.

### 5. Pourquoi la commande df -T, qui affiche le type de système de fichier des partitions, ne fonctionne-telle pas sur notre disque ?
> Le disque n'étant pas encore "mounted", la commande ne le voit pas. En effet cette commande n'affiche que les disques montés.

### 6. Faites en sorte que les deux partitions créées soient montées automatiquement au démarrage de la machine, respectivement dans les points de montage /data et /win :
> Pour que les deux partitions créées soient montées automatiquement au démarrage de la machine, on créé 2 points de montage (/data et /win) et on écrit dans le fichier **fstlab** ceci :

![2](https://user-images.githubusercontent.com/62430323/77758944-26377380-7034-11ea-83e5-135be2b80be1.png)

### 7. Utilisez la commande mount puis redémarrez votre VM pour valider la configuration :
> On tape les 2 commandes suivante : 
> > > ``sudo mount --target /data /dev/sdb1``
> > > ``sudo mount --target /win /dev/sdb2`` 

>  Puis on redémarre et on fait ``df -T``: 

![3](https://user-images.githubusercontent.com/62430323/77758949-2768a080-7034-11ea-8358-1a62a6914a5e.png)


## Exercice 2. Personnalisation de GRUB

### 2. Modifiez le fichier /etc/default/grub pour que le menu de GRUB s’affiche pendant 10 secondes ; passé ce délai, le premier OS du menu doit être lancé automatiquement :
> On écrit  ``Grub_aohfdqs = countdown`` et ``Grub_timeout = 10`` dans le fichier /etc/default/grub.
> On a vérifié que les changements aient bien été pris en compte.

### 3. Lancez la commande update-grub :

![TP6Admin-1](https://user-images.githubusercontent.com/62430323/77759132-7ca4b200-7034-11ea-8f4f-9473971c326b.PNG)

### 4. Redémarrez votre VM pour valider que les changements ont bien été pris en compte :
> On observe bien un timer de 10 secondes (en haut à gauche de l'écran) au démarrage de la VM.

### 5. On va augmenter la résolution de GRUB et de notre VM. Cherchez sur Internet le ou les paramètres à rajouter au fichier grub :
> On rajoute dans le fichier /etc/default/grub ``Grub_gfxmode = 1600x900``.
> On observe bien un changement de résolution au démarrage.

### 6. On va à présent ajouter un fond d’écran. Il existe un paquet en proposant quelques uns : grub2-splash-images (après installation, celles-ci sont disponibles dans /usr/share/images/grub).
> On rajoute dans le fichier /etc/default/grub ``GRUB_BACKGROUND="/usr/share/images/BonsaiTridentMaple.tga"``.
> On observe bien le fond d'écran au démarrage.

![TP6Admin-2](https://user-images.githubusercontent.com/62430323/77759134-7d3d4880-7034-11ea-9813-6c5b5527bb05.PNG)

### 7. Il est également possible de configurer des thèmes. On en trouve quelques uns dans les dépôts (grub2-themes-*). Installez-en un.
> On procède de la même façon qu'à la question précédente (on installe le paquet) et on rajoute dans le fichier /etc/default/grub ``GRUB_THEME="/boot/grub/themes/theme.txt""``

## Exercice 3. Noyau

### 1. Commencez par installer le paquet build-essential, qui contient tous les outils nécessaires (compilateurs, bibliothèques) à la compilation de programmes en C (entre autres).
> On écrit la commande ``sudo apt install build-essential``

###  4. Compilez le module à l’aide de la commande make, puis installez-le à l’aide de la commande make install :
 >On écrit la commande ``make``, puis  ``sudo make install ``.

### 5. Chargez le module ; vérifiez dans le journal du noyau que le message "La fonction init_module() est appelée" a bien été inscrit, synonyme que le module a été chargé ; confirmez avec la commande lsmod :
> Pour charger le module, on utilise la commande ``sudo insmod hello.ko`` et ensuite on regarde dans le journal du noyau si "La fonction init_module() est appelée" est bien écrit, ce qui est le cas :
![4](https://user-images.githubusercontent.com/62430323/77759012-49622300-7034-11ea-83c1-d83c22924ab7.png)
> Enfin pour confirmer qu'il est bien été chargé dans le noyau, on utilise la commande ``lsmod`` qui va lister le contenue de /proc/modules.
> On peut aussi utiliser la commande suivante, à la place de ``lsmod`` :
![5](https://user-images.githubusercontent.com/62430323/77759017-4a935000-7034-11ea-9cb6-dd2c53707f13.png)
### 6. Utilisez la commande modinfo pour obtenir des informations sur le module hello.ko ; vous devriez notamment voir les informations figurant dans le fichier C :
> En exécutant la commande ``modinfo hello.ko`` on observe ceci :

![6](https://user-images.githubusercontent.com/62430323/77759021-4bc47d00-7034-11ea-9380-3be19ed012fa.png)

> On retrouve effectivement la version, la description, l'auteur ou encore la licence, qui sont tous présent dans le **.c**.

### 7. Déchargez le module ; vérifiez dans le journal du noyau que le message ”La fonction cleanup_module() est appelée” a bien été inscrit, synonyme que le module a été déchargé ; confirmez avec la commande lsmod :
> On décharge le module grâce à la commande ``sudo rmmod hello.ko``:
![7](https://user-images.githubusercontent.com/62430323/77759025-4cf5aa00-7034-11ea-8d03-77972d360fb7.png)
> De la même manière que tout à l'heure, on observe cette fois-ci que le module **hello** n'est plus présent dans /proc/module :
![8](https://user-images.githubusercontent.com/62430323/77759030-4e26d700-7034-11ea-92ae-5eb0f6b50585.png)

### 8. Pour que le module soit chargé automatiquement au démarrage du système, il faut l’inscrire dans le fichier /etc/modules. Essayez, et vérifiez avec la commande lsmod après redémarrage de la machine.

> On déplace le module hello.ko dans /etc/modules , qui est un fichier, en écrivant ``hello`` dedans, grâce à la commande ``echo "hello" >> /etc/modules``.
> On a bien retrouvé le module au redémarrage lorsqu'on a excécuté ``lsmod``.
