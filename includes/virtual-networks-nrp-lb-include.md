## Подсистема балансировки нагрузки
Подсистема балансировки нагрузки используется, когда необходимо масштабировать приложения. Типичные сценарии развертывания включают приложения, работающие на нескольких экземплярах виртуальной машины. Перед экземплярами виртуальной машины расположена подсистема балансировки нагрузки, которая помогает распределять сетевой трафик между различными экземплярами.

![Сетевая карта на одной виртуальной машине](./media/resource-groups-networking/figure8.png)

| Свойство | Описание |
|---|---|
| *frontendIPConfigurations* | Балансировщик нагрузки может включать в себя один или несколько внешних IP-адресов, которые также называются виртуальными IP-адресами. Эти IP-адреса служат для приема входящего трафика и могут быть общедоступными или частными. |
|*backendAddressPools* | Это IP-адреса, связанные с сетевыми картами виртуальных машин, между которыми распределяется нагрузка. |
|*loadBalancingRules* | Свойство правила сопоставляет указанное сочетание внешнего IP-адреса и порта с набором серверных IP-адресов и портов. С помощью одного определения ресурса балансировщика нагрузки можно задать несколько правил балансировки нагрузки, каждое из которых отражает сочетание внешнего IP-адреса и порта с серверным IP-адресом и портом, связанными с виртуальными машинами. Правило сопоставляет один порт в интерфейсном пуле с несколькими виртуальными машинами во внутреннем пуле. |  
| *Пробы* | Позволяют отслеживать работоспособность экземпляров виртуальных машин. В случае сбоя пробы работоспособности экземпляр виртуальной машины автоматически перестает использоваться. |
| *inboundNatRules* | Правила NAT, определяющие входящий трафик, который проходит через внешний IP-адрес и передается на внутренний IP-адрес определенному экземпляру виртуальной машины. Правило NAT сопоставляет один порт в интерфейсном пуле с одной виртуальной машиной во внутреннем пуле. | 

Пример шаблона балансировщика нагрузки в формате Json:

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "dnsNameforLBIP": {
	      "type": "string",
	      "metadata": {
	        "description": "Unique DNS name"
	      }
	    },
	    "location": {
	      "type": "string",
	      "allowedValues": [
	        "East US",
	        "West US",
	        "West Europe",
	        "East Asia",
	        "Southeast Asia"
	      ],
	      "metadata": {
	        "description": "Location to deploy"
	      }
	    },
	    "addressPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/16",
	      "metadata": {
	        "description": "Address Prefix"
	      }
	    },
	    "subnetPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/24",
	      "metadata": {
	        "description": "Subnet Prefix"
	      }
	    },
	    "publicIPAddressType": {
	      "type": "string",
	      "defaultValue": "Dynamic",
	      "allowedValues": [
	        "Dynamic",
	        "Static"
	      ],
	      "metadata": {
	        "description": "Public IP type"
	      }
	    }
	  },
	  "variables": {
	    "virtualNetworkName": "virtualNetwork1",
	    "publicIPAddressName": "publicIp1",
	    "subnetName": "subnet1",
	    "loadBalancerName": "loadBalancer1",
	    "nicName": "networkInterface1",
	    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
	    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
	    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	    "nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]",
	    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	    "backEndIPConfigID": "[concat(variables('nicId'),'/ipConfigurations/ipconfig1')]"
	  },
	  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameforLBIP')]"
        }
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
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
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnetRef')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(variables('lbID'), '/backendAddressPools/LoadBalancerBackend')]"
                }
              ],
              "loadBalancerInboundNatRules": [
                {
                  "id": "[concat(variables('lbID'),'/inboundNatRules/RDP')]"
                }
              ]
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[variables('publicIPAddressID')]"
              }
            }
          }
        ],
        "backendAddressPools": [
          {
            "name": "loadBalancerBackEnd"
          }
        ],
        "inboundNatRules": [
          {
            "name": "RDP",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPort": 3389,
              "backendPort": 3389,
              "enableFloatingIP": false
            }
          }
        ]
      }
    }
	  ]
	}

### Дополнительные ресурсы

Дополнительную информацию см. в описании [REST API балансировщика нагрузки](https://msdn.microsoft.com/library/azure/mt163651.aspx).

<!---HONumber=AcomDC_1223_2015-->