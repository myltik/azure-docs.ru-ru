## Azure DNS

Azure DNS является службой размещения для доменов DNS, предоставляющей разрешение имен с помощью инфраструктуры Microsoft Azure.


| Свойство | Описание | Образец значения |
|---|---|---|
| **DNSzones** | Информация о зоне домена для размещения записей DNS определенного домена. | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com"| 


### Наборы записей DNS

Зоны DNS имеют дочерний объект, называемый набором записей. Наборы записей представляют собой упорядоченную по типу коллекцию записей узла для зоны DNS. Типы записей: A, AAAA, CNAME, MX, NS, SOA, SRV и TXT.

| Свойство | Описание | Образец значения |
|---|---|---|
| Файл , | Тип записи IPv4 | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/A/www |
| AAAA | Тип записи IPv6| /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/AAAA/hostrecord |
| CNAME | Тип записи канонического имени <sup>1</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/CNAME/www |
| MX | Тип записи почты | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/MX/mail |
| NS | Тип записи имени сервера | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/NS/ |
| SOA | Тип записи SOA (начальная запись зоны) <sup>2</sup> | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SOA |
| SRV | Тип записи службы | /subscriptions/{guid}/.../providers/Microsoft.Network/dnszones/contoso.com/SRV |

<sup>1</sup> допускает только одно значение на каждый набор записей.

<sup>2</sup> допускает только один тип записи SOA на каждую зону DNS.

Пример зоны DNS в формате Json:

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "newZoneName": {
	      "type": "String",
	      "metadata": {
	          "description": "The name of the DNS zone to be created."
	      }
	    },
	    "newRecordName": {
	      "type": "String",
	      "defaultValue": "www",
	      "metadata": {
	          "description": "The name of the DNS record to be created.  The name is relative to the zone, not the FQDN."
	      }
	    }
	  },
	  "resources": 
	  [
	    {
	      "type": "microsoft.network/dnszones",
	      "name": "[parameters('newZoneName')]",
	      "apiVersion": "2015-05-04-preview",
	      "location": "global",
	      "properties": {
	      }
	    },
	    {
	      "type": "microsoft.network/dnszones/a",
		  "name": "[concat(parameters('newZoneName'), concat('/', parameters('newRecordName')))]",
      	"apiVersion": "2015-05-04-preview",
      	"location": "global",
	  	"properties": 
	  	{
        	"TTL": 3600,
			"ARecords": 
			[
			    {
				    "ipv4Address": "1.2.3.4"
				},
				{
				    "ipv4Address": "1.2.3.5"
				}
			]
	  	},
	  	"dependsOn": [
        	"[concat('Microsoft.Network/dnszones/', parameters('newZoneName'))]"
      	]
    	}
	  	]
	}

## Дополнительные ресурсы

Ознакомьтесь с [документацией по REST API для зон DNS ](https://msdn.microsoft.com/library/azure/mt130626.aspx), чтобы получить дополнительные сведения.

Ознакомьтесь с [документацией по REST API для наборов записей DNS ](https://msdn.microsoft.com/library/azure/mt130627.aspx), чтобы получить дополнительные сведения.

<!---HONumber=AcomDC_0128_2016-->