## Виртуальная сеть
Ресурсы виртуальных сетей (VNET) и подсетей позволяют определить периметр безопасности для рабочих нагрузок, выполняемых в Azure. Виртуальная сеть характеризуется набором адресных пространств, которые также называются блоками CIDR.

>[AZURE.NOTE] Сетевым администраторам знакомы нотации CIDR. Если вы не знакомы с методом CIDR, [узнайте больше о нем](http://whatismyipaddress.com/cidr).

![Виртуальная сеть с несколькими подсетями](./media/resource-groups-networking/Figure4.png)

У виртуальных сетей есть следующие свойства.

|Свойство|Описание|Примеры значений|
|---|---|---|
|**addressSpace**|Коллекция префиксов адресов, составляющих виртуальную сеть в нотации CIDR.|192\.168.0.0/16|
|**Подсети**|Коллекция подсетей, составляющих виртуальную сеть.|См. раздел [Подсети](#Subnets) ниже.|
|**ipAddress**|IP-адрес, назначенный объекту. Это свойство доступно только для чтения.|104\.42.233.77|

### Подсети
Подсеть является дочерним ресурсом виртуальной сети, который помогает определить сегменты адресных пространств в пределах блока CIDR на основе префиксов IP-адресов. Сетевые карты можно добавлять в подсети и подключать к виртуальным машинам, обеспечивая сетевые подключения для различных рабочих нагрузок.

У подсетей есть следующие свойства.

|Свойство|Описание|Примеры значений|
|---|---|---|
|**addressPrefix**|Одноадресный префикс, определяющий подсеть в нотации CIDR.|192\.168.1.0/24|
|**networkSecurityGroup**|Группа безопасности сети для подсети.|См. раздел [Группы безопасности сети](#Network-Security-Group).|
|**routeTable**|Таблица маршрутизации подсети.|См. раздел [Определяемый пользователем маршрут](#Route-table).|
|**ipConfigurations**|Коллекция объектов конфигурации IP, используемых сетевыми картами, подключенными к подсети.|См. раздел [Определяемый пользователем маршрут](#Route-table).|


Пример виртуальной сети в формате JSON:

	{
	    "name": "TestVNet",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/virtualNetworks",
	    "location": "westus",
	    "tags": {
	        "displayName": "VNet"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "addressSpace": {
	            "addressPrefixes": [
	                "192.168.0.0/16"
	            ]
	        },
	        "subnets": [
	            {
	                "name": "FrontEnd",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "addressPrefix": "192.168.1.0/24",
	                    "networkSecurityGroup": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
	                    },
	                    "routeTable": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
	                    },
	                    "ipConfigurations": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
	                        },
	                        ...]
	                }
	            },
	            ...]
	    }
	}

### Дополнительные ресурсы

- Более подробная информация о [виртуальной сети](../articles/virtual-network/virtual-networks-overview.md).
- Прочитайте [справочную документацию по REST API](https://msdn.microsoft.com/library/azure/mt163650.aspx) для виртуальных сетей.
- Прочитайте [справочную документацию по REST API](https://msdn.microsoft.com/library/azure/mt163618.aspx) для подсетей.

<!---HONumber=AcomDC_0323_2016-->