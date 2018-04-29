---
title: Интеграция центра обработки данных Azure Stack. Публикация конечных точек | Документация Майкрософт
description: Сведения о публикации конечных точек Azure Stack в центре обработки данных
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 014dbf6ff6d30bfb988c404bfe35693fe68f22fd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Интеграция центра обработки данных Azure Stack. Публикация конечных точек
Для ролей инфраструктуры в Azure Stack настраиваются виртуальные IP-адреса (VIP). Эти виртуальные IP-адреса выделяются из пула общедоступных IP-адресов. Каждый виртуальный IP-адрес защищается списком управления доступом (ACL) на уровне программно определяемой сети. Для дополнительной защиты решения списки управления доступом применяются и на физических коммутаторах (стоечные коммутаторы и BMC). Для каждой конечной точки создается DNS-запись во внешней зоне DNS, которая указана во время развертывания.


На следующей схеме с архитектурой показаны несколько уровней сети и списков управления доступом.

![Изображение структуры](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Порты и протоколы (входящие)

Набор виртуальных IP-адресов инфраструктуры, необходимых для публикации конечных точек Azure Stack во внешних сетях. В таблице *конечных точек (VIP)* для каждой конечной точки указаны назначение, используемый порт и протокол. Конечные точки, необходимые для поставщиков дополнительных ресурсов, например для поставщика ресурсов SQL, описаны в документации по развертыванию соответствующих ресурсов.

Здесь не указаны виртуальные IP-адреса для внутренней инфраструктуры, так как они не используются для публикации Azure Stack.

> [!NOTE]
> Пользователям виртуальные IP-адреса предоставляются динамически, и они самостоятельно управляют этим процессом без согласования с оператором Azure Stack.


|Конечная точка (виртуальный IP-адрес)|Запись A на узле DNS|Протокол|порты;|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443|
|Портал (для администратора)|Adminportal.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (для администратора)|Adminmanagement.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443<br>30024|
|Портал (для пользователя)|Portal.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (для пользователя)|Management.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443<br>30024|
|График|Graph.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443|
|Список отзыва сертификатов|Crl.*&lt;регион>.&lt;полное доменное имя>*|HTTP|80|
|DNS|&#42;.*&lt;регион>.&lt;полное доменное имя>*|TCP или UDP|53|
|Хранилище ключей (для пользователя)|&#42;.vault.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443|
|Хранилище ключей (для администратора)|&#42;.adminvault.*&lt;регион>.&lt;полное доменное имя>*|HTTPS|443|
|Очередь службы хранилища|&#42;.queue.*&lt;регион>.&lt;полное доменное имя>*|HTTP<br>HTTPS|80<br>443|
|Таблица службы хранилища|&#42;.table.*&lt;регион>.&lt;полное доменное имя>*|HTTP<br>HTTPS|80<br>443|
|Большой двоичный объект хранилища|&#42;.blob.*&lt;регион>.&lt;полное доменное имя>*|HTTP<br>HTTPS|80<br>443|
|Поставщик ресурсов SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300–44304|
|Поставщик ресурсов MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300–44304|
|Служба приложений|&#42;.appservice.*&lt;регион>.&lt;полное доменное имя>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;регион>.&lt;полное доменное имя>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;регион>.&lt;полное доменное имя>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;регион>.&lt;полное_доменное_имя>*|TCP, UDP|21, 1021, 10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>Порты и URL-адреса (исходящие)

Стек Azure поддерживает только прозрачные прокси-серверы. Если в вашем развертывании прозрачный прокси-сервер перенаправляет соединения к традиционному прокси-серверу, необходимо разрешить следующие порты и URL-адреса для исходящей связи:


|Назначение|URL-адрес|Протокол|порты;|
|---------|---------|---------|---------|
|Удостоверение|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com|HTTP<br>HTTPS|80<br>443|
|Синдикация Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Обновления и исправления|https://&#42;.azureedge.net|HTTPS|443|
|Регистрация|https://management.azure.com|HTTPS|443|
|Использование|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|
|Защитник Windows|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|



## <a name="next-steps"></a>Дополнительная информация

[Требования к инфраструктуре открытых ключей Azure Stack](azure-stack-pki-certs.md)