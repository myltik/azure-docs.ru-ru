## Группа безопасности сети
Ресурс NSG позволяет создать периметр безопасности для рабочих нагрузок путем реализации разрешающих и запрещающих правил. Такие правила могут быть применены к виртуальной машине, сетевой карте или подсети.

|Свойство|Описание|Примеры значений|
|---|---|---|
|**Подсети**|Список идентификаторов подсетей, к которым применена группа безопасности сети.|/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd|
|**securityRules**|Список правил безопасности, которые определяют группу безопасности сети.|См. раздел [Правило безопасности](#Security-rule) ниже.|
|**defaultSecurityRules**|Список правил безопасности по умолчанию в каждой группе безопасности сети.|См. раздел [Правила безопасности по умолчанию](#Default-security-rules) ниже.|

- **Правило безопасности**: для NSG можно определить несколько правил безопасности. Каждое правило может разрешать или запрещать различные типы трафика.

### Правило безопасности
Правило безопасности является дочерним ресурсом группы безопасности сети, содержащим описанные ниже свойства.

|Свойство|Описание|Примеры значений|
|---|---|---|
|**description**|Описание правила.|Разрешить входящий трафик для всех виртуальных машин в подсети X.|
|**protocol**|Протокол правила для сопоставления.|TCP, UDP или *|
|**sourcePortRange**|Диапазон портов источника правила для сопоставления.|80, 100-200, *|
|**destinationPortRange**|Диапазон портов назначения правила для сопоставления.|80, 100-200, *| 
|**sourceAddressPrefix**|Префикс адреса источника правила для сопоставления.|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**destinationAddressPrefix**|Префикс адреса назначения правила для сопоставления.|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**direction**|Направление трафика правила для сопоставления.|inbound или outbound|
|**priority**|Приоритет правила. Правила проверяются в порядке приоритета. Когда применяется правило, соответствие дополнительным правилам не проверяется.|10, 100, 65000|
|**access**|Тип доступа, применяемый при соответствии правилу.|allow или deny|

Пример группы безопасности сети в формате JSON:

	{
	    "name": "NSG-BackEnd",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/networkSecurityGroups",
	    "location": "westus",
	    "tags": {
	        "displayName": "NSG - Front End"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "securityRules": [
	            {
	                "name": "rdp-rule",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "description": "Allow RDP",
	                    "protocol": "Tcp",
	                    "sourcePortRange": "*",
	                    "destinationPortRange": "3389",
	                    "sourceAddressPrefix": "Internet",
	                    "destinationAddressPrefix": "*",
	                    "access": "Allow",
	                    "priority": 100,
	                    "direction": "Inbound"
	                }
	            }
	        ],
	        "defaultSecurityRules": [
	            { [...],
	        "subnets": [
	            {
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
	            }
	        ]
	    }
	}

### Правила безопасности по умолчанию
Правила безопасности по умолчанию имеют те же свойства, что и правила безопасности. Они существуют для обеспечения базовых подключений между ресурсами, к которым применены группы безопасности сети. Убедитесь, что знаете, какие существуют [правила безопасности по умолчанию](../articles/virtual-network/virtual-networks-nsg.md#Default-Rules).

### Дополнительные ресурсы

- Более подробная информация о [группах безопасности сети](../articles/virtual-network/virtual-networks-nsg.md).
- Прочитайте [справочную документацию по REST API](https://msdn.microsoft.com/library/azure/mt163615.aspx) для групп безопасности сети.
- Прочитайте [справочную документацию по REST API](https://msdn.microsoft.com/library/azure/mt163580.aspx) для правил безопасности.

<!---HONumber=AcomDC_0323_2016-->