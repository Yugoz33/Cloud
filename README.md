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


