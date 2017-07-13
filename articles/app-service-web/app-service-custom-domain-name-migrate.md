---
title: "Перенос активного личного домена в службу приложений Azure | Документация Майкрософт"
description: "Узнайте, как перенести личный домен, который уже назначен работающему сайту, в приложение в службе приложений Azure без простоев."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 5f6537a45bcb092b5ef463e8069b9fc5582c14c2
ms.contentlocale: ru-ru
ms.lasthandoff: 06/14/2017


---
<a id="migrate-an-active-custom-domain-to-azure-app-service" class="xliff"></a>

# Перенос активного личного домена в службу приложений Azure

В этой статье показано, как перенести активный личный домен в [службу приложений Azure](../app-service/app-service-value-prop-what-is.md) без простоев.

При переносе активного сайта и его имени домена в службу приложений это имя домена уже обслуживает текущий трафик, и вам не нужны простои в работе DNS во время переноса. Поэтому для такого случая необходимо заблаговременно привязать имя домена к приложению Azure для проверки домена.

<a id="prerequisites" class="xliff"></a>

## Предварительные требования

В этой статье предполагается, что вам уже известно, как [вручную сопоставить личный домен со службой приложений](app-service-web-tutorial-custom-domain.md).

<a id="bind-the-domain-name-preemptively" class="xliff"></a>

## Заблаговременная привязка доменного имени

Заблаговременно привязывая личный домен, вы выполняете обе из приведенных ниже задач перед внесением каких-либо изменений в записи DNS:

- проверка принадлежности домена;
- включение доменного имени для приложения.

Когда впоследствии вы измените запись DNS, чтобы она указывала на приложение службы приложений, клиенты будут перенаправляться со старого веб-сайта в это приложение службы приложений без простоев при разрешении имен DNS.

Для этого сделайте вот что.

1. Сначала создайте проверочную запись типа TXT с реестром DNS, выполнив действия, описанные в разделе [Создание записей DNS](app-service-web-tutorial-custom-domain.md).
Для дополнительной записи типа TXT применяется соглашение, в соответствии с которым &lt;*поддомен*>.&lt;*корневой_домен*> сопоставляется с адресом &lt;*имя_приложения*>.azurewebsites.net.
Примеры см. в таблице ниже.  

    <table cellspacing="0" border="1">
    <tr>
    <th>Пример полного доменного имени</th>
    <th>Узел, для которого задается TXT</th>
    <th>Значение TXT</th>
    </tr>
    <tr>
    <td>contoso.com (корневой домен)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>имя_приложения</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com (поддомен)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>имя_приложения</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com (с подстановочным знаком)</td>
    <td>awverify\*.contoso.com</td>
    <td>&lt;<i>имя_приложения</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. Затем добавьте имя личного домена в приложение Azure, выполнив действия, описанные в разделе [Включение имени личного домена для приложения](app-service-web-tutorial-custom-domain.md#enable-a).

    Теперь личный домен включен в приложении Azure. Единственное, что остается сделать, это обновить запись DNS с помощью регистратора домена.

3. Наконец, обновите запись DNS домена, чтобы она указывала на приложение Azure, как показано в разделе [Создание записей DNS](app-service-web-tutorial-custom-domain.md).

    Пользовательский трафик должен перенаправляться в приложение Azure сразу после того, как произойдет распространение DNS.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
Узнайте, как защитить имя личного домена с помощью протокола HTTPS, [приобретя SSL-сертификат в Azure](web-sites-purchase-ssl-web-site.md) или [используя SSL-сертификат из другого источника](app-service-web-tutorial-custom-ssl.md).

> [!NOTE]
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
>
>

[Приступая к работе с Azure DNS](../dns/dns-getstarted-create-dnszone.md)  
[Создание записей DNS для веб-приложения в пользовательском домене](../dns/dns-web-sites-custom-domain.md)  
[Делегирование домена в Azure DNS](../dns/dns-domain-delegation.md)

