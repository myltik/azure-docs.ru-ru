---
title: "Управление зонами DNS с помощью интерфейса командной строки | Документация Майкрософт"
description: "Зонами DNS можно управлять с помощью Azure CLI. Как обновлять, удалять и создавать зоны DNS в Azure DNS"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 6fe10238adea0cbe1a47c05767930a363645028b

---

# <a name="how-to-manage-dns-zones-using-cli"></a>Как управлять записями DNS с помощью интерфейса командной строки

> [!div class="op_single_selector"]
> * [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

В этом руководстве показано, как управлять ресурсами зоны DNS с помощью кроссплатформенного Azure CLI.

В данных инструкциях используется интерфейс командной строки Microsoft Azure (CLI). Чтобы использовать команды Azure DNS, обновите интерфейс командной строки Azure до последней версии (0.9.8 или более поздней). Введите команду `azure -v`, чтобы узнать, какая версия Azure CLI установлена на компьютере. (доступны версии для Windows, Linux или Mac). Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md).

DNS Azure является исключительно службой диспетчера ресурсов Azure. У нее нет API ASM. Потребуется убедиться, что Azure CLI настроен для использования режима Resource Manager. Для этого воспользуйтесь командой `azure config mode arm`.

Если вы видите сообщение "*ошибка: "dns" не является командой Azure*", скорее всего вы используете Azure CLI в режиме ASM, а не Resource Manager.

## <a name="create-a-new-dns-zone"></a>Создание зоны DNS

Чтобы создать новую зону DNS для размещения домена, ознакомьтесь с разделом [Создание зоны Azure DNS с помощью интерфейса командной строки](dns-getstarted-create-dnszone-cli.md).

## <a name="get-a-dns-zone"></a>Получение зоны DNS

Чтобы получить зону DNS, используйте команду `azure network dns zone show`.

```azurecli
azure network dns zone show myresourcegroup contoso.com
```

В результате этой операции будет возвращена зона DNS с ее идентификатором, количеством наборов записей и тегами.

## <a name="list-dns-zones"></a>Перечисление зон DNS

Чтобы получить зоны DNS в группе ресурсов, используйте команду `azure network dns zone list`.

```azurecli
azure network dns zone list myresourcegroup
```

## <a name="update-a-dns-zone"></a>Обновление зоны DNS

Изменить ресурс зоны DNS можно с помощью командлета `azure network dns zone set`. При этом ни один набор записей DNS в пределах зоны не обновляется (см. статью [Управление записями и наборами записей DNS с помощью PowerShell](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. Сейчас область применения ограничена "тегами" Azure Resource Manager для ресурса зоны. Подробные сведения см. в разделе [Об Etag и тегах](dns-getstarted-create-dnszone.md#tagetag).

```azurecli
azure network dns zone set myresourcegroup contoso.com -t prod=value2
```

## <a name="delete-a-dns-zone"></a>Удаление зоны DNS

Зоны DNS можно удалить с помощью команды `azure network dns zone delete`. Для этой операции можно использовать необязательный параметр *-q* , который подавляет запрос на подтверждение удаления зоны DNS.

Прежде чем удалить зону DNS в Azure DNS, необходимо удалить все наборы записей, за исключением записей NS и SOA в корне зоны, которые были созданы автоматически вместе с зоной.

```azurecli
azure network dns zone delete myresourcegroup contoso.com
```

## <a name="next-steps"></a>Дальнейшие действия

Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset-cli.md) , чтобы начать разрешение имен для домена Интернета.




<!--HONumber=Nov16_HO3-->


