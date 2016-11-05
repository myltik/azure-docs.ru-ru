---
title: Управление зонами DNS с помощью CLI | Microsoft Docs
description: Зонами DNS можно управлять с помощью Azure CLI. Как обновлять, удалять и создавать зоны DNS в Azure DNS
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# Как управлять записями DNS с помощью интерфейса командной строки
> [!div class="op_single_selector"]
> * [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)
> 
> 

В этом руководстве показано, как управлять ресурсами зоны DNS с помощью кроссплатформенного Azure CLI.

В данных инструкциях используется интерфейс командной строки Microsoft Azure (CLI). Чтобы использовать команды Azure DNS, обновите интерфейс командной строки Azure до последней версии (0.9.8 или более поздней). Введите команду `azure -v`, чтобы узнать, какая версия Azure CLI установлена на компьютере. (доступны версии для Windows, Linux или Mac). Дополнительные сведения см. в статье [Установка Azure CLI](../xplat-cli-install.md).

DNS Azure является исключительно службой диспетчера ресурсов Azure. У нее нет API ASM. Потребуется убедиться, что Azure CLI настроен для использования режима Resource Manager. Для этого воспользуйтесь командой `azure config mode arm`.<BR> Если вы видите сообщение "*ошибка: "dns" не является командой Azure*", скорее всего вы используете Azure CLI в режиме ASM, а не Resource Manager.

## Создание зоны DNS
Чтобы создать новую зону DNS для размещения домена, ознакомьтесь с разделом [Создание зоны Azure DNS с помощью интерфейса командной строки](dns-getstarted-create-dnszone-cli.md).

## Получение зоны DNS
Чтобы получить зону DNS, используйте команду `azure network dns zone show`.

    azure network dns zone show myresourcegroup contoso.com

В результате этой операции будет возвращена зона DNS с ее идентификатором, количеством наборов записей и тегами.

## Перечисление зон DNS
Чтобы получить зоны DNS в группе ресурсов, используйте команду `azure network dns zone list`.

    azure network dns zone list myresourcegroup

## Обновление зоны DNS
Изменить ресурс зоны DNS можно с помощью командлета `azure network dns zone set`. При этом ни один набор записей DNS в пределах зоны не обновляется (см. [Управление записями DNS](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. В текущее время область применения ограничена "тегами" диспетчера ресурсов Azure для ресурса зоны. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#tagetag).

    azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Удаление зоны DNS
Зоны DNS можно удалить с помощью команды `azure network dns zone delete`. Для этой операции можно использовать необязательный параметр *-q*, который подавляет запрос на подтверждение удаления зоны DNS.

Прежде чем удалить зону DNS в Azure DNS, необходимо удалить все наборы записей, за исключением записей NS и SOA в корне зоны, которые были созданы автоматически вместе с зоной.

    azure network dns zone delete myresourcegroup contoso.com 



## Дальнейшие действия
Создав зону DNS, создайте [наборы записей и записи](dns-getstarted-create-recordset-cli.md), чтобы начать разрешение имен для домена Интернета.

<!---HONumber=AcomDC_0817_2016-->