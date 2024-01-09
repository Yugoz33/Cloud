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
