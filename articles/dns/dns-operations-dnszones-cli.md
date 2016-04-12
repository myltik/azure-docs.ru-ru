<properties 
   pageTitle="Операции с зонами DNS с помощью интерфейса командной строки | Microsoft Azure" 
   description="Зонами DNS можно управлять с помощью Azure CLI. Как обновлять, удалять и создавать зоны DNS в Azure DNS" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Как управлять записями DNS с помощью интерфейса командной строки

> [AZURE.SELECTOR]
- [Интерфейс командной строки Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)

В этом руководстве показано, как управлять ресурсами зоны DNS с помощью кроссплатформенного Azure CLI.

>[AZURE.NOTE] DNS Azure является исключительно службой диспетчера ресурсов Azure. У нее нет API ASM. Поэтому необходимо будет убедиться, что Azure CLI настроен на использование режима диспетчера ресурсов, с помощью команды azure config mode arm.

>Если вы видите сообщение «ошибка: "dns" не является командой Azure», скорее всего вы используете Azure CLI в режиме ASM, а не диспетчера ресурсов.
 
## Создание зоны DNS

Чтобы создать зону DNS для размещения домена, используйте командлет `azure network dns zone create`.

	azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

В результате этой операции в Azure DNS будет создана зона DNS. При необходимости можно указать массив тегов диспетчера ресурсов Azure. Дополнительные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

Имя зоны должно быть уникальным в пределах группы ресурсов, а зона не должна существовать, иначе операция завершится ошибкой.

То же имя зоны можно использовать повторно в другой группе ресурсов или другой подписке Azure. Если имеется несколько зон с одним именем, каждому экземпляру будут назначены разные адреса серверов имен, и только один экземпляр может быть делегирован из родительского домена. Подробнее: [Делегирование домена Azure DNS](dns-domain-delegation.md).

## Получение зоны DNS

Чтобы получить зону DNS, используйте командлет `azure network dns zone show`.

	azure network dns zone show myresourcegroup contoso.com

В результате этой операции будет возвращена зона DNS с ее идентификатором, количеством наборов записей и тегами.


## Перечисление зон DNS

Чтобы получить зоны DNS в группе ресурсов, используйте командлет `azure network dns zone list`.

	azure network dns zone list myresourcegroup


## Обновление зоны DNS

Изменить ресурс зоны DNS можно с помощью командлета `azure network dns zone set`. При этом ни один набор записей DNS в пределах зоны не обновляется (см. [Управление записями DNS](dns-operations-recordsets.md)). Эта команда используется только для обновления свойств самого ресурса зоны. В текущее время область применения ограничена "тегами" диспетчера ресурсов Azure для ресурса зоны. Подробные сведения см. в разделе [Etag и теги](dns-getstarted-create-dnszone.md#Etags-and-tags).

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Удаление зоны DNS

Зоны DNS можно удалить с помощью командлета `azure network dns zone delete`.
 
Прежде чем удалить зону DNS в Azure DNS, необходимо удалить все наборы записей, за исключением записей NS и SOA в корне зоны, которые были созданы автоматически вместе с зоной.

	azure network dns zone delete myresourcegroup contoso.com 

Для этой операции можно использовать необязательный параметр "-q", который подавляет запрос подтверждения на удаление зоны DNS.


## Дальнейшие действия


Узнайте, как [управлять записями DNS](dns-operations-recordsets-cli.md) и [автоматизировать операции с помощью пакета SDK для .NET](dns-sdk.md).

<!---HONumber=AcomDC_0309_2016-->