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
}```