## <a name="route-tables"></a>Таблицы маршрутов
Ресурсы таблиц маршрутов содержат маршруты, используемые для направления трафика в рамках инфраструктуры Azure. Вы можете использовать определяемые пользователем маршруты (UDR) для отправки всего трафика из определенной подсети в виртуальный модуль, например брандмауэр или систему обнаружения атак (IDS). Таблицу маршрутизации можно связать с подсетью. 

Таблицы маршрутизации обладают следующими свойствами.

| Свойство | Описание | Примеры значений |
| --- | --- | --- |
| **routes** |Коллекция определяемых пользователем маршрутов в таблице маршрутизации. |См. раздел [Определяемые пользователем маршруты](#User-defined-routes). |
| **Подсети** |Коллекция подсетей, к которым применяется таблица маршрутизации. |См. раздел [Подсети](#Subnets). |

### <a name="user-defined-routes"></a>Определяемые пользователем маршруты
Вы можете создать определяемые пользователем маршруты, чтобы указать, куда следует отправлять трафик, по его адресу назначения. Маршрут можно считать определением шлюза по умолчанию, построенным на основе адреса назначения сетевого пакета.

Определяемые пользователем маршруты содержат следующие свойства. 

| Свойство | Описание | Примеры значений |
| --- | --- | --- |
| **addressPrefix** |Префикс адреса или полный IP-адрес назначения. |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |Тип устройства, на которое будет отправляться трафик. |VirtualAppliance, VPN-шлюз, Интернет |
| **nextHopIpAddress** |IP-адрес следующего прыжка. |192.168.1.4 |

Пример таблицы маршрутов в формате JSON:

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>Дополнительные ресурсы
* Более подробная информация об [определяемых пользователем маршрутах](../articles/virtual-network/virtual-networks-udr-overview.md).
* Прочитайте [справочную документацию по REST API](https://msdn.microsoft.com/library/azure/mt502549.aspx) для таблиц маршрутов.
* Прочитайте [справочную документацию по REST API](https://msdn.microsoft.com/library/azure/mt502539.aspx) для определяемых пользователем маршрутов (UDR).



<!--HONumber=Nov16_HO3-->


