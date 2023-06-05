## CanBoot SB2040

 
Qu'est-ce que CanBoot ?
 
CanBoot est un chargeur de démarrage conçu pour ARM Cortex-M mcu. Ce chargeur de démarrage a été conçu à l'origine pour les nœuds CAN à utiliser avec Klipper. Le chargeur de démarrage lui-même utilise la couche d'abstraction matérielle de Klipper, qui minimise l'empreinte mémoire. Outre CAN, CanBoot prend désormais également en charge les interfaces USB et UART. Prend actuellement en charge lpc176x, stm32 et rp2040 trois mcu. La prise en charge CAN est actuellement limitée aux appareils stm32 f-series et rp2040.
 
Klipper prend déjà en charge CanBoot, qui peut graver directement le firmware via CANBUS. Après avoir utilisé CanBoot pour mettre à jour le micrologiciel klipper pour la carte SHT36/42, il n'est pas nécessaire de connecter le câble USB, et le micrologiciel peut être directement gravé tout en maintenant la connexion CAN existante, ce qui peut mettre à jour le micrologiciel de la carte outil CAN plus commodément et efficacement.
 
##  1. Compiler le firmware de démarrage CanBoot
 
1. Entrez le terminal SSH
2. Exécutez la commande suivante
 
```bash
git clone https://github.com/Arksine/CanBoot
cd CanBoot
```
 
![ 1 ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/1.png)
 
 
```bash
make clean
make menuconfig
```

#### **SB2040 & ERCF** 
 
?> S'il n'y a pas de RP2040 dans votre option CanBoot, veuillez Installer le dernier CanBoot
 
![ sb2040 ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/sb2040.png)
 
Une fois la configuration terminée, appuyez sur la touche "Q", puis appuyez sur la touche "Y" pour quitter et enregistrer. Exécutez la commande suivante pour compiler le firmware
 
```bash
make
```
 
![ 3 ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/3.png)
 
* Si ``Creating hex file out/canboot.bin`` ou ``Creating uf2 file out/canboot.uf2`` apparaît comme l'image ci-dessus , la compilation est réussie.


##  2. Graver le firmware de démarrage CanBoot

#### **SB2040 & ERCF** 
 
?> S'il n'y a pas de RP2040 dans votre option CanBoot, veuillez extraire le dernier CanBoot (entrez `git pull` dans le répertoire CanBoot pour extraire le dernier CanBoot)
 
1. Vérifiez s'il est connecté au mode de gravure BOOT du SB2040
 
Appuyez et maintenez la touche BOOT de la carte SB2040, puis connectez l'usb à l'ordinateur hôte
 
![ boot ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/boot.png)
 
```bash
lsusb
```
 
Exécutez la commande ci-dessus pour vérifier s'il existe une ligne `` ID 2e8a: 0003 Raspberry Pi RP2 Boot`` , sinon, veuillez vérifier le câble USB (n'oubliez pas d'appuyer et de maintenir la touche BOOT avant de vous connecter)
 
![ lsusb ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/lsusb.png)
 
2. Burn
 
```bash
cd  ~/CanBoot/
make flash FLASH_DEVICE=2e8a:0003
```
 
L'exécution de la commande ci-dessus peut vous demander d'entrer un mot de passe, entrez simplement le mot de passe de l'utilisateur actuel, et il ne sera pas visible lorsque vous entrez le mot de passe. Après avoir tapé, appuyez sur Entrée
 
Si l'image suivante apparaît, la gravure est réussie
 
![ flash ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/flash.png)
 
3. Vérifiez
 
Si la configuration correcte est compilée et gravée avec succès, le voyant de la carte SB2040 clignotera à une certaine fréquence ! ! !
 
![ statusled ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/statusled.png)

##  3. Gravez le firmware Klipper avec CanBoot pour la première fois
 
* Assurez-vous que votre SHT36/42 est correctement connecté à l'UTOC ou à un autre appareil CAN
 
1. Tirez sur le dernier klipper
 
```bash
cd  ~/klipper
git pull
```
 
![ 5 ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/5.png)
 
2. Configurez le dernier micrologiciel klipper
 
```bash
make clean
make menuconfig
```

#### **SB2040 & ERCF** 
 
1. Commencez par compiler le firmware Klipper et configurez-le comme indiqué ci-dessous
 
![ sb2040-k ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/sb2040-k.png)
 
* Appuyez sur la touche "Q" après avoir configuré comme l'image ci-dessus, puis appuyez sur la touche "Y" pour quitter et enregistrer.
 
2. Compiler le micrologiciel klipper
 
```bash
make
```
 
![ 7 ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/7.png)
 
* Si ``Creating hex file out/klipper.bin`` apparaît comme l'image ci-dessus , la compilation est réussie.
 
3. Après avoir connecté les fils, il est recommandé de couper l'alimentation de toute la machine et de la rallumer. Ensuite, vous pouvez utiliser CanBoot pour flasher le firmware. Entrez d'abord la commande suivante.
 
?> Après la mise sous tension, ce voyant d'état doit clignoter à une certaine fréquence !!! S'il ne clignote pas, veuillez graver à nouveau le micrologiciel de démarrage CanBoot ! !
 
<img src="https://github.com/Eloura74/SB2040_Francais/blob/main/images/statusled.png" alt="statusled" style="zoom:67%;" />
 
```
python3 ~/klipper/lib/canboot/flash_can.py -q
```
 
La partie en surbrillance ``365f54003b9d`` dans la figure ci-dessous est l'uuid de ce SHT, et cet uuid est différent pour chaque carte. L'uuid ne changera pas après la gravure du firmware sur la même carte SB2040.
 
![ uuid ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/uuid.png)
 
?> Si vous ne trouvez pas le CAN ID, vérifiez :
 
* Si le câblage est correct, par exemple, si CANH et CANL sont inversés ou pas en bon contact.
* Le capuchon du cavalier 120Ω sur la carte SB2040 est-il inséré ?
* Votre noyau d'image prend-il en charge CAN.
* Vérifiez si la compilation CanBoot est correcte. S'il n'y a pas d'erreur, vous pouvez réessayer de flasher CanBoot. Pour les étapes de flashage, veuillez vous référer à :[Cliquez ici pour accéder à la section CanBoot](#2-graver-le-firmware-de-démarrage-canboot)


* Si vous ne trouvez toujours pas l'ID, vous pouvez utiliser l'ordinateur hôte pour graver à nouveau le micrologiciel.
 
4. Graver le micrologiciel Klipper via CANBUS
 
* ``fea6a45462e9`` dans la commande ci-dessous doit être remplacé par l'UUID que vous avez obtenu à l'étape précédente.
 
```bash
python3 lib/canboot/flash_can.py -i can0 -f ./out/klipper.bin -u fea6a45462e9
```
 
![ 10 ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/10.png)
 
* Si ``CAN Flash Success`` apparaît dans l'image ci-dessus , cela signifie que la programmation est réussie.
 
?> Si le CanBoot a été gravé plusieurs fois et que l'ID CanBoot est introuvable, vous pouvez utiliser la méthode suivante pour graver le micrologiciel :
 
1. Vérifiez s'il est connecté au mode de gravure BOOT du SB2040
 
Appuyez et maintenez la touche BOOT de la carte SB2040, puis connectez l'usb à l'ordinateur hôte.
 
![ boot ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/boot.png)
 
```bash
lsusb
```
 
Exécutez la commande ci-dessus pour vérifier s'il existe une ligne `` ID 2e8a:0003 Raspberry Pi RP2 Boot `` , sinon, veuillez vérifier le câble USB (n'oubliez pas d'appuyer et de maintenir la touche BOOT avant de vous connecter).
 
![ lsusb ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/lsusb.png)
 
2. Brûler
 
```bash
cd  ~/klipper/
make flash FLASH_DEVICE=2e8a:0003
```
 
L'exécution de la commande ci-dessus peut vous demander d'entrer un mot de passe, entrez simplement le mot de passe de l'utilisateur actuel, et il ne sera pas visible lorsque vous entrez le mot de passe. Après avoir entré la commande, appuyez sur Entrée.
 
Si l'image suivante apparaît, la gravure est réussie.
 
![ flash ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/flash.png)
 
3. Vérifiez
 
Si la configuration correcte est compilée et gravée avec succès, le voyant de la carte SB2040 sera toujours allumé ! ! !
 
![ statusled ](https://github.com/Eloura74/SB2040_Francais/blob/main/images/statusled.png)
 

##  4. Le firmware avec CanBoot a été gravé
 
> Si vous avez gravé **Canboot boot firmware** et **Firmware with Canboot**, vous devez mettre à jour le firmware klipper à l'avenir, suivez simplement les étapes ci-dessous
* Obtenir le dernier klipper
 
```bash
cd  ~/klipper
git pull
```
 
* Compiler le dernier klipper
 
```bash
make menuconfig
make
```

* Burn klipper pour SHT36/42
* ``fea6a45462e9`` dans la commande suivante doit être remplacé par l'UUID que vous avez demandé
 
```bash
python3 ~/klipper/lib/canboot/flash_can.py -i can0 -q
```
 
```
python3 ~/klipper/lib/canboot/flash_can.py -i can0 -f ./out/klipper.bin -u fea6a45462e9
```
