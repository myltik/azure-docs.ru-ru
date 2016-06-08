<properties
   pageTitle="Создание набора записей и записей зоны DNS с помощью интерфейса командной строки | Microsoft Azure"
   description="Как создать записи узла для Azure DNS. Настройка наборов записей и записей с помощью интерфейса командной строки."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Создание записей и наборов записей DNS с помощью интерфейса командной строки

> [AZURE.SELECTOR]
- [Портал Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)


В этой статье показано, как создать записи и наборы записей с помощью интерфейса командной строки. После создания зоны DNS необходимо добавить DNS-записи для вашего домена. Для этого сначала требуется изучить DNS-записи и наборы записей.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Создание набора записей и записи

В этом разделе будет показано, как создать записи и набор записей. В этом примере создается запись с относительным именем *www* в зоне DNS *contoso.com*. Полное доменное имя записи — *www.contoso.com*. Тип записи — *A*, а срок жизни составляет 60 секунд. По завершении этого шага будет создан пустой набор записей.

Чтобы создать набор записей на вершине зоны (в этом случае "contoso.com"), используйте имя записи "@", включая кавычки. Это общее соглашение при работе с DNS.

### 1\. Создание набора записей

Чтобы создать набор записей, используйте `azure network dns record-set create`. Укажите группу ресурсов, имя зоны, относительное имя набора записей, тип записи и срок жизни. Если параметр --ttl не определен, используется значение по умолчанию 4 (в секундах). По завершении этого шага будет создан пустой набор записей www.
	
*Использование: network dns record-set create <resource-group> <dns-zone-name> <name> <type> <ttl>*

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\. Добавление записей

Чтобы использовать недавно созданный набор записей *www*, в него нужно добавить записи. Записи добавляются в наборы записей с помощью команды `azure network dns record-set add-record`.

Параметры для добавления записей в набор записей зависят от типа набора записей. Например, при использовании набора записей типа *A* вы сможете указать записи только с параметром `-a <IPv4 address>`.

Записи *A* (IPv4) можно добавить в набор записей *www* с помощью следующей команды:

*Использование: network dns record-set add-record <resource-group> <dns-zone-name> <record-set-name> <type>*

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## Дополнительные примеры типов записей

В следующих примерах показано, как создать набор записей каждого типа, содержащий одну запись.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## Дальнейшие действия

Сведения об управлении записями и набором записей см. в статье [Как управлять записями DNS с помощью интерфейса командной строки](dns-operations-recordsets-cli.md).

Дополнительные сведения об Azure DNS см. в статье [Обзор Azure DNS](dns-overview.md).

<!----HONumber=AcomDC_0525_2016-->
