<properties
   pageTitle="Создание набора записей и записей зоны DNS с помощью портала Azure | Microsoft Azure"
   description="Сведения о создании записей узла для Azure DNS, а также наборов записей и записей с помощью портала Azure"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Создание наборов записей и записей DNS с помощью портала Azure


> [AZURE.SELECTOR]
- [Портал Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Интерфейс командной строки Azure](dns-getstarted-create-recordset-cli.md)


В этой статье показано, как создать записи и наборы записей с помощью портала Azure. После создания зоны DNS необходимо добавить DNS-записи для вашего домена. Для этого сначала требуется изучить DNS-записи и наборы записей.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## Создание набора записей и записи

Следующий пример описывает создание набора записей и отдельных записей с помощью портала Azure. Мы используем запись DNS типа "A".

1. Войдите на портал Azure.

2. Перейдите к колонке **Зона DNS**, где требуется создать набор записей.

3. В верхней части этой колонки щелкните **Набор записей**, чтобы открыть колонку **Add record set** (Добавление набора записей).
 
	![новый набор записей](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. В колонке **Add record set** (Добавление набора записей) укажите имя для набора записей. Например, набор записей можно назвать **www**.
  
	![добавление набора записей](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Введите или выберите тип создаваемой записи. Например, **A**.

6. Задайте значение **TTL**. По умолчанию для портала используется 1 час.

7. Добавьте IP-адреса, по одному на строке. При использовании предлагаемого имени набора записей и указанного выше типа IPv4-адреса добавляются в запись A для набора записей www.

8. После добавления всех IP-адресов нажмите кнопку **ОК** в нижней части колонки. Создается набор записей DNS.


## Дальнейшие действия

Сведения об управлении записями и набором записей см. в статье [Создание записей и наборов записей DNS и управление ими с помощью портала Azure](dns-operations-recordsets-portal.md).

Дополнительные сведения об Azure DNS см. в статье [Обзор Azure DNS](dns-overview.md).

<!---HONumber=AcomDC_0518_2016-->