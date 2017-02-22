---
title: "Настройте личное доменное имя для веб-приложения в службе приложений Azure, использующей диспетчер трафика для балансировки нагрузки."
description: "Используйте личное доменное имя для веб-приложения в службе приложений Azure, которая включает в себя диспетчер трафика."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: wpickett
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 532a953c02ef40eb55705eac885d74c5574deed5


---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Настройка личного доменного имени для веб-приложения в службе приложений Azure, использующей диспетчер трафика
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

В этой статье содержатся общие инструкции по использованию личного доменного имени со службой приложений Azure, которая использует диспетчер трафика для балансировки нагрузки.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Общие сведения о записях DNS
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Настройка веб-приложений для режима Standard
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Добавление записи DNS для пользовательского домена
> [!NOTE]
> Если вы приобрели домен через веб-приложения службы приложений Azure, см. только последний шаг статьи [Покупка домена для веб-приложений](custom-dns-web-site-buydomains-web-app.md).
> 
> 

Для сопоставления личного домена с веб-приложением в службе приложений Azure необходимо добавить новую запись в таблицу DNS об этом домене с помощью инструментов регистратора, у которого вы приобрели доменное имя. Выполните следующие действия, чтобы найти и использовать средства DNS.

1. Выполните вход в систему регистратора доменных имен с использованием своей учетной записи и найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто ссылку на эту страницу можно найти, открыв сведения своей учетной записи и найдя такую ссылку, как **Мои домены**.
2. Когда найдена страница управления для вашего доменного имени, найдите ссылку, позволяющую изменять записи DNS. Она может иметь название **Файл зоны**, **Записи DNS** или **Дополнительно**.
   
   * Страница, скорее всего, уже будет содержать несколько созданных записей, например запись, сопоставляющую **@** или \* со страницей "парковки домена". Она также может содержать записи для распространенных поддоменов, таких как **www**.
   * На этой странице будут упоминаться **записи CNAME**или будет предоставлен раскрывающийся список для выбора типа записи. На ней также могут упоминаться другие записи, такие как **записи A** и **записи MX**. В некоторых случаях записи CNAME могут называться по-другому, например **записью псевдонима**.
   * Страница будет также содержать поля, позволяющие **сопоставить** значения из поля **Имя узла** или **Доменное имя** с другим доменным именем.
3. Несмотря на особенности каждого регистратора, как правило, вы сопоставляете имя *своего* личного домена (например, **contoso.com**) *с* доменным именем диспетчера трафика (**contoso.trafficmanager.net**), которое используется в вашем веб-приложении.
   
   > [!NOTE]
   > Кроме того, если запись уже используется и вам нужно заблаговременно привязать к ней свое приложение, создайте дополнительную запись CNAME. Например, чтобы заблаговременно привязать **www.contoso.com** к веб-приложению, создайте запись CNAME на основе **awverify.www** для **contoso.trafficmanager.net**. Затем можно добавить к веб-приложению фрагмент www.contoso.com, не изменяя запись CNAME www. Дополнительные сведения см. в статье [Создание записей DNS для веб-приложения в пользовательском домене][CREATEDNS].
   > 
   > 
4. По завершении добавления или изменения записей DNS у своего регистратора сохраните эти изменения.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Включение диспетчера трафика
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию см. в [центре разработчиков Node.js](/develop/nodejs/).

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md



<!--HONumber=Jan17_HO1-->


