---
title: Использование программы redis-cli с кэшем Redis для Azure | Документы Майкрософт
description: Сведения об использовании программы redis-cli с кэшем Redis для Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182284"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Использование программы командной строки Redis с кэшем Redis для Azure

*redis cli.exe* является популярной программой командной строки для взаимодействия с кэшем Redis в качестве клиента. Ее также можно использовать с кэшем Redis для Azure.

Программа доступна для платформ Windows — скачайте [программы командной строки Redis для Windows](https://github.com/MSOpenTech/redis/releases/). 

Если вы хотите запустить программу командной строки на другой платформе, скачайте кэш Redis по адресу: [http://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Сбор сведений для доступа к кэшу

Существует три способа сбора сведений, необходимых для доступа к кэшу.

1. С помощью Azure CLI и команды [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys).
2. С помощью Azure PowerShell и командлета [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1).
3. С помощью портала Azure.

В этом разделе вы будете получать ключи с портала Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Включение доступа для redis cli.exe

При использовании кэша Redis для Azure только один SSL-порт (6380) включен по умолчанию. Программа командной строки `redis-cli.exe` не поддерживает SSL. У вас есть два варианта конфигурации для использования программы.

1. [Включите порт, отличный от SSL (6379)](cache-configure.md#access-ports) - **такая конфигурация не рекомендуется**, так как ключи доступа отправляются по TCP в виде открытого текста. Это изменение может нарушить доступ к кэшу. Единственным сценарием, где может потребоваться эта конфигурация, является просто доступ к кэшу теста.

2. Скачайте и установите [stunnel](https://www.stunnel.org/downloads.html).

    Выполните команду **stunnel GUI Start**, чтобы запустить сервер.

    Щелкните правой кнопкой мыши значок сервера stunnel и выберите пункт **Show Log Window** (Открыть окно журнала).

    В меню "Log Window" (Окно журнала) выберите **Конфигурация** > **Изменить конфигурацию**, чтобы открыть текущий файл конфигурации.

    В разделе **Определения службы** добавьте следующую запись для программы *redis cli.exe*. Вместо `yourcachename` вставьте фактическое имя кэша. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Сохраните и закройте файл конфигурации. 
  
    В меню "Log Window" (Окно журнала) stunnel выберите **Конфигурация** > **Перезагрузить конфигурацию**.


## <a name="connect-using-the-redis-command-line-tool"></a>Подключитесь с помощью программы командной строки Redis.

При использовании stunnel запустите *redis cli.exe* и для подключения к кэшу передайте только *порт* и *ключ доступа* (первичный или вторичный).

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel с redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Если вы используете кэш теста с **небезопасным** портом, отличным от SSL, запустите `redis-cli.exe` и для подключения к кэшу теста передайте *имя узла*, *порт* и *ключ доступа* (первичный или вторичный).

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel с redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Дополнительная информация

Узнайте больше об использовании [консоли Redis](cache-configure.md#redis-console) для выполнения команд.

