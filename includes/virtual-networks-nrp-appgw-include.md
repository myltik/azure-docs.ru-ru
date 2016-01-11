## Шлюз приложений

Шлюз приложений обеспечивает управляемое Azure решение по балансировке нагрузки HTTP на основе технологии балансировки нагрузки на 7 уровне. Балансировка нагрузки приложения позволяет использовать правила маршрутизации для сетевого трафика в зависимости от HTTP.

| Свойство | Описание | 
|---|---|
| **backendAddressPools** | Список IP-адресов внутренних серверов. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные, виртуальные либо частные IP-адреса. |
| **backendHttpSettingsCollection** | У каждого пула есть такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле. |
| **frontendPorts** | Общий порт, открытый в шлюзе приложений. Трафик поступает в этот порт, а затем перенаправляется на один из внутренних серверов. |
| **httpListeners** | Прослушиватель имеет внешний порт, протокол (HTTP или HTTPS, с учетом регистра) и имя SSL-сертификата (если настраивается разгрузка SSL). |
| **requestRoutingRules** | Связывает прослушиватель и пул внутренних серверов, а также определяет, в какой пул внутренних серверов следует направлять трафик. В настоящее время работает только по принципу циклического перебора. |


Пример шаблона шлюза приложений в формате Json:

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "metadata": {
	        "description": "Location to deploy to"
	      }
	    },
	    "addressPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/16",
	      "metadata": {
	        "description": "Address prefix for the Virtual Network"
	      }
	    },
	    "subnetPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/28",
	      "metadata": {
	        "description": "Subnet prefix"
	      }
	    },
	    "skuName": {
	      "type": "string",
	      "allowedValues": [
	        "Standard_Small",
	        "Standard_Medium",
	        "Standard_Large"
	      ],
	      "defaultValue": "Standard_Medium",
	      "metadata": {
	        "description": "Sku Name"
	      }
	    },
	    "capacity": {
	      "type": "int",
	      "defaultValue": 2,
	      "metadata": {
	        "description": "Number of instances"
	      }
	    },
	    "backendIpAddress1": {
	      "type": "string",
	      "metadata": {
	        "description": "IP Address for Backend Server 1"
	      }
	    },
	    "backendIpAddress2": {
	      "type": "string",
	      "metadata": {
	        "description": "IP Address for Backend Server 2"
	      }
	    }
	  },
	  "variables": {
	    "applicationGatewayName": "applicationGateway1",
	    "publicIPAddressName": "publicIp1",
	    "virtualNetworkName": "virtualNetwork1",
	    "subnetName": "appGatewaySubnet",
	    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    	"subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
    	"publicIPRef": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
    	"applicationGatewayID": "[resourceId('Microsoft.Network/applicationGateways',variables('applicationGatewayName'))]",
		"apiVersion": "2015-05-01-preview"
  	},
 	 "resources": [
    	{
    	  "apiVersion": "[variables('apiVersion')]",
    	  "type": "Microsoft.Network/publicIPAddresses",
    	  "name": "[variables('publicIPAddressName')]",
    	  "location": "[parameters('location')]",
    	  "properties": {
    	    "publicIPAllocationMethod": "Dynamic"
    	  }
    	},
    	{
    	  "apiVersion": "[variables('apiVersion')]",
    	  "type": "Microsoft.Network/virtualNetworks",
    	  "name": "[variables('virtualNetworkName')]",
    	  "location": "[parameters('location')]",
    	  "properties": {
    	    "addressSpace": {
    	      "addressPrefixes": [
    	        "[parameters('addressPrefix')]"
    	      ]
    	    },
    	    "subnets": [
    	      {
    	        "name": "[variables('subnetName')]",
    	        "properties": {
    	          "addressPrefix": "[parameters('subnetPrefix')]"
    	        }
    	      }
    	    ]
    	  }
    	},
    	{
    	  "apiVersion": "[variables('apiVersion')]",
    	  "name": "[variables('applicationGatewayName')]",
    	  "type": "Microsoft.Network/applicationGateways",
    	  "location": "[parameters('location')]",
    	  "dependsOn": [
    	    "[concat('Microsoft.Network/virtualNetworks/', variables	('virtualNetworkName'))]",
    	    "[concat('Microsoft.Network/publicIPAddresses/', variables	('publicIPAddressName'))]"
    	  ],
    	  "properties": {
    	    "sku": {
    	      "name": "[parameters('skuName')]",
    	      "tier": "Standard",
    	      "capacity": "[parameters('capacity')]"
    	    },
    	    "gatewayIPConfigurations": [
    	      {
    	        "name": "appGatewayIpConfig",
    	        "properties": {
    	          "subnet": {
    	            "id": "[variables('subnetRef')]"
    	          }
    	        }
    	      }
    	    ],
    	    "frontendIPConfigurations": [
    	      {
    	        "name": "appGatewayFrontendIP",
    	        "properties": {
    	          "PublicIPAddress": {
    	            "id": "[variables('publicIPRef')]"
    	          }
    	        }
    	      }
    	    ],
    	    "frontendPorts": [
    	      {
    	        "name": "appGatewayFrontendPort",
    	        "properties": {
    	          "Port": 80
    	        }
    	      }
    	    ],
    	    "backendAddressPools": [
    	      {
    	        "name": "appGatewayBackendPool",
    	        "properties": {
    	          "BackendAddresses": [
    	            {
    	              "IpAddress": "[parameters('backendIpAddress1')]"
    	            },
    	            {
    	              "IpAddress": "[parameters('backendIpAddress2')]"
    	            }
    	          ]
    	        }
    	      }
    	    ],
    	    "backendHttpSettingsCollection": [
    	      {
    	        "name": "appGatewayBackendHttpSettings",
    	        "properties": {
    	          "Port": 80,
    	          "Protocol": "Http",
    	          "CookieBasedAffinity": "Disabled"
    	        }
    	      }
    	    ],
    	    "httpListeners": [
    	      {
    	        "name": "appGatewayHttpListener",
    	        "properties": {
    	          "FrontendIPConfiguration": {
    	            "Id": "[concat(variables('applicationGatewayID'), '/	frontendIPConfigurations/appGatewayFrontendIP')]"
    	          },
    	          "FrontendPort": {
    	            "Id": "[concat(variables('applicationGatewayID'), '/	frontendPorts/appGatewayFrontendPort')]"
    	          },
    	          "Protocol": "Http",
    	          "SslCertificate": null
    	        }
    	      }
    	    ],
    	    "requestRoutingRules": [
    	      {
    	        "Name": "rule1",
    	        "properties": {
    	          "RuleType": "Basic",
    	          "httpListener": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	httpListeners/appGatewayHttpListener')]"
    	          },
    	          "backendAddressPool": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	backendAddressPools/appGatewayBackendPool')]"
    	          },
    	          "backendHttpSettings": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	backendHttpSettingsCollection/	appGatewayBackendHttpSettings')]"
    	          }
    	        }
    	      }
    	    ]
    	  }
    	}
  	]	
	}


### Дополнительные ресурсы

Подробнее об этом см. в описании [REST API шлюза приложений](https://msdn.microsoft.com/library/azure/mt299388.aspx).

<!---HONumber=AcomDC_1223_2015-->