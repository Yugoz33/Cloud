🌞 Une fois que c'est fait, pour vérifier la bonne installatation...

on va toute de suite créer un "Groupe de ressources" ou "Resource Group"

utilisez la commande suivante pour créer votre premier Resource Group
```
az>> az group create --name efreitp2 --location francecentral
{
  "id": "/subscriptions/15934968-657a-44fa-ae2b-c59094e54274/resourceGroups/efreitp2",
  "location": "francecentral",
  "managedBy": null,
  "name": "efreitp2",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": "Microsoft.Resources/resourceGroups"
}
```

🌞 Créez une VM depuis la WebUI

et vérifiez que vous pouvez vous y connecter en SSH en utilisant une clé

![machine1](tp%20cloud%20p1.png)

🌞 Créez une VM depuis le Azure CLI

en utilisant uniquement la commande az donc
assurez-vous que dès sa création terminée, vous pouvez vous connecter en SSH en utilisant une IP publique
vous devrez préciser :

quel utilisateur doit être créé à la création de la VM
le fichier de clé utilisé pour se connecter à cet utilisateur
comme ça, dès que la VM pop, on peut se co en SSH !

```bash
az>> vm create --name yugoz --resource-group efreitp2 --image Ubuntu2204 --admin-username yugoz --ssh-key-values "C:\Users\hugoc\.ssh\id_rsa.pub"
```
```
{
  "fqdns": "",
  "id": "/subscriptions/15934968-657a-44fa-ae2b-c59094e54274/resourceGroups/efreitp2/providers/Microsoft.Compute/virtualMachines/yugoz",
  "location": "francecentral",
  "macAddress": "00-22-48-38-C7-09",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "51.103.52.25",
  "resourceGroup": "efreitp2",
  "zones": ""
}
```
```
PS C:\Users\hugoc> ssh yugoz@51.103.52.25
The authenticity of host '51.103.52.25 (51.103.52.25)' can't be established.
ED25519 key fingerprint is SHA256:WYfJAehhEEc8gm6nel9kMsp8U2FaFtk/h3jTzfc2KEE.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '51.103.52.25' (ED25519) to the list of known hosts.
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 6.2.0-1018-azure x86_64)
```
Walinux agent et cloud-init verification

``` 
service walinuxagent status
● walinuxagent.service - Azure Linux Agent
     Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
    Drop-In: /usr/lib/systemd/system/walinuxagent.service.d
             └─10-Slice.conf, 11-CPUAccounting.conf, 13-MemoryAccounting.conf
     Active: active (running) since Tue 2024-01-09 15:06:36 UTC; 7min ago
   Main PID: 716 (python3)
      Tasks: 6 (limit: 4013)
     Memory: 44.2M
        CPU: 2.869s
     CGroup: /system.slice/walinuxagent.service
             ├─ 716 /usr/bin/python3 -u /usr/sbin/waagent -daemon
             └─1029 python3 -u bin/WALinuxAgent-2.9.1.1-py3.8.egg -run-exthandlers            
```
```
yugoz@yugoz:~$ cloud-init status
status: done
```
🌞 Créez deux VMs depuis le Azure CLI

assurez-vous qu'elles ont une IP privée (avec ip a)
elles peuvent se ping en utilisant cette IP privée
deux VMs dans un LAN quoi !

```
yugoz@yugoz:~$ ping 10.0.0.4
PING 10.0.0.4 (10.0.0.4) 56(84) bytes of data.
64 bytes from 10.0.0.4: icmp_seq=1 ttl=64 time=2.54 ms
64 bytes from 10.0.0.4: icmp_seq=2 ttl=64 time=1.12 ms
64 bytes from 10.0.0.4: icmp_seq=3 ttl=64 time=1.23 ms
64 bytes from 10.0.0.4: icmp_seq=4 ttl=64 time=0.885 ms
^C
--- 10.0.0.4 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 0.885/1.443/2.542/0.646 ms
```
II. cloud-init
🌞 Tester cloud-init

en créant une nouvelle VM et en lui passant ce fichier cloud-init.txt au démarrage
pour ça, utilisez une commande az vm create

utilisez l'option --custom-data /path/to/cloud-init.txt
```bash
az>> vm create --name tpcloudinit --resource-group efreitp2 --image Ubuntu2204 --custom-data "C:\cloud tp\Cloud\cloud-init.txt"
```

🌞 Vérifier que cloud-init a bien fonctionné

connectez-vous en SSH à la VM nouvellement créée
vous devriez observer qu'un nouvel utilisateur a été créé, avec le bon password et la bonne clé
```
PS C:\Users\hugoc> ssh hugo@51.103.69.15
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 6.2.0-1018-azure x86_64)
```
```
hugo@tpcloudinit:~$ su hugo
Password:
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```
I. Création image custom

1. Créer une VM
🌞 Créez une VM

uniquement avec la commande az

OS de votre choix

le Ubuntu proposé par défaut par Azure c'est très bien
on sait que c'est stable, pour notre travail ici c'est cool


conf ajoutée à la création :

un user
une clé publique pour s'y connecter
```bash
az>> vm create --name hugo --resource-group efreitp2 --image Ubuntu2204 --admin-username hugo --ssh-key-values "C:\Users\hugoc\.ssh\id_rsa.pub"
```
🌞 Installer docker à la machine

suivez les instructions de la doc officielle
assurez-vous que le service démarre automatiquement lorsque la machine démarre (avec une commande systemctl)
```
hugo@hugo:~$ docker --version
Docker version 24.0.2, build cb74dfc
hugo@hugo:~$ sudo systemctl enable docker
Synchronizing state of docker.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable docker
hugo@hugo:~$
```
🌞 Améliorer la configuration du serveur SSH

on pense surtout à la sécurité ici
je vous laisse trouver un bon guide sur internet, hésitez pas à m'appeler pour me demander mon avis !

j'ai suivi se tuto
```
https://www.digitalocean.com/community/tutorials/how-to-harden-openssh-on-ubuntu-20-04
```
🌞 Une fois que tout est fait : généraliser la VM

ce qu'on appelle "généraliser" c'est le fait de la remettre dans un état comme si elle n'avait jamais été lancée
comme ça, elle pourra servir de base à la création d'autres VMs (c'est le but !)
les étapes :

reset la conf réseau
on reset cloud-init pour qu'il se relance au prochain boot
on reset l'agent Azure (wagent) pour qu'il se lance au prochaine boot comme un premier boot
on supprime l'historique de commandes
```
# Suppression des confs réseau qui auraient pu être créées au premier boot
sudo rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg /etc/cloud/cloud.cfg.d/99-installer.cfg /etc/cloud/cloud.cfg.d/subiquity-disable-cloudinit-networking.cfg
sudo rm -f /etc/cloud/ds-identify.cfg
sudo rm -f /etc/netplan/*.yaml

# Conf déjà effectuée normalement mais au cas où : on demande à l'agent Azure d'utiliser cloud-init
sudo sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sudo sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sudo sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sudo sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cat <<EOF | sudo tee -a /etc/waagent.conf
Provisioning.Agent=auto
EOF

# On reset cloud-init
sudo cloud-init clean --logs --seed
sudo rm -rf /var/lib/cloud/

# On reset l'agent Azure
sudo systemctl stop walinuxagent.service
sudo rm -rf /var/lib/waagent/
sudo rm -f /var/log/waagent.log
sudo waagent -force -deprovision+user

# Suppression de l'historique de commande
sudo rm -f ~/.bash_history
```
🌞 Pour ça, suivez les commandes az suivantes :
```
az vm  create --resource-group efreitp2 --name prout --image "/subscriptions/15934968-657a-44fa-ae2b-c59094e54274/resourceGroups/efreitp2/providers/Microsoft.Compute/ga
lleries/efreigallery/images/efreidef/versions/1.0.0" --security-type TrustedLaunch --ssh-key-values "C:\Users\hugoc.ssh\id_rsa.pub" --admin-username hugo
```



🌞 Sur votre PC, écrivez un fichier cloud-init.txt

réutiliser le fichier que je vous ai fourni plus haut pour créer un user
ajoutez à ce fichier ce qu'il faut pour que cloud-init lance un conteneur NGINX :

ainsi, dès que la VM pop, PAF ! Un serveur web déjà dispo dans un conteneur
une ligne très simple suffit, par exemple :


```bash
systemctl start docker 
docker run -d -p 80:80 nginx
```

voici a quoi ressemble mon fichier cloudinit.txt
```
#cloud-config
users:
  - name: hugo
    primary_group: hugo
    groups: sudo
    shell: /bin/bash
    sudo: ALL=(ALL) NOPASSWD:ALL
    lock_passwd: false
    passwd: $6$B.tYaooC0/jQHtbC$O5LDuFNc3NMClzz9EIDuu8c0wXT6ib.fC5HRFIlCFqAZEXIg6rSadIUmL3o88LVzmVr5E1aI4K.XceDxrgPgF.
    ssh_authorized_keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDa6I8jCpILOkzG9lDZpXluwt3+hV2rhq9Mf9eHQU4DpOKfNT3tzH0mflGZyUwbETwy3cxHERbmCDdKuDtIEMfu3RRJ8EOx0H9FxUTocuSxo9SZCt1ta64nAJqkheDzIZAA1FdUOT5AS2L9zzOw6JZdCx0mgN0kIdB5SEvii0T5nZ3nFdJ4WZ9ftgm/R37isyk7mGh0kIWLMuiQMJNkJ5ShskG2PpDXXxEIrA+nV2o2u5FkRcFVQvcfVV3FAKpan2zghwxXi1LSTFaQWlVPqRueMZhCcH74khSmxLYqpIHSrw3Q6PgZ5B6yJO8ghcCurWWFCCfS6Dvt+uiGIkhvIDQIypsemBJJxvoxHgZegOyHmPFDcOlPNY0uIijnVfVQGMpa/QDxLe5xaznXK7RpElDl20hECJlaKFgFxhvARkkBX0Pq73oySvw+3w2Q8J1t+2Q0Qj+BnC6KTPNepn0S/5CEtTcIKHsjY5yuVotdANPDpS8FUKM9DAfVou3nZi/PFr/4Exm7F5t7v6vmSiYvLJmtRbsPMO7stxlxlVVkDlR2j/zde5ueYimjBAupuXWBQaSeqA8clIge09Ew40tdH1zhkEeqVI65MkWMX5IOi0GYv0oOhnUbh0g4xla8l6MYe4TZM8FFgcoNdhtEXm2qt4g/aVY7mfF3V+wWSEN5gDOsbQ== hugoc@Acer-Yugoz
runcmd:
  - docker run -d -p 80:80 nginx
```
```bash
 az vm create -n tpnginx --resource-group efreitp2 --image "/subscriptions/15934968-657a-44fa-ae2b-c59094e54274/resourceGroups/efreitp2/providers/Microsoft.Compute/galleries/efreigallery/images/efreidef/versions/1.0.0" --custom-data "C:\cloud tp\Cloud\cloud-init.txt" --security-type TrustedLaunch
```
```bash
hugo@tpnginx:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS         PORTS                               NAMES
8842921d76a2   nginx     "/docker-entrypoint.…"   11 seconds ago   Up 8 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   amazing_sublime
```

🌞 Avec une comande az, créez une nouvelle machine

elle doit utiliser notre image custom
elle doit fournir votre fichier cloud-init.txt à la VM pour que le service cloud-init applique son contenu
une fois qu'elle a démarré, vérifiez que le serveur web tourne
![machine1](fini1.png)




