---
title: Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure | Документация Майкрософт
description: Узнайте, как добавить имеющееся DNS-имя личного домена (именного домена) для веб-приложения, серверной части мобильного приложения или приложения API в службе приложений Azure.
keywords: служба приложений, служба приложений Azure, сопоставление доменов, доменное имя, существующий домен, имя узла
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 431268082b24d23289188f5422cd596dc5f37d30
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-web-apps"></a>Руководство. Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure

[Веб-приложения Azure](app-service-web-overview.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. В этом руководстве показано, как сопоставить имеющееся DNS-имя личного домена с веб-приложением Azure.

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Сопоставление поддомена (например, `www.contoso.com`) с помощью записи CNAME.
> * Сопоставление корневого домена (например, `contoso.com`) с помощью записи A.
> * Сопоставление домена с подстановочным знаком (например, `*.contoso.com`) с помощью записи CNAME.
> * Автоматизация сопоставления доменов с помощью скриптов.

Для сопоставления настраиваемого DNS-имени со службой приложений можно использовать **запись CNAME** или **запись A**. 

> [!NOTE]
> Мы рекомендуем использовать записи CNAME для всех настраиваемых DNS-имен, кроме корневого домена (например, `contoso.com`).

Сведения о том, как перенести активный веб-сайт и его DNS-имя домена в службу приложений, см. в статье [Перенос активного DNS-имени в службу приложений Azure](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

* [Создайте приложение службы приложений](/azure/app-service/) или используйте приложение, созданное для работы с другим руководством.
* Приобретите доменное имя и убедитесь, что вы имеете доступ к реестру DNS поставщика домена (например, GoDaddy).

  Например, чтобы добавить записи DNS для `contoso.com` и `www.contoso.com`, необходимо настроить параметры DNS для корневого домена `contoso.com`.

  > [!NOTE]
  > Если у вас нет доменного имени, вы можете [купить его на портале Azure](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Подготовка приложения

Чтобы сопоставить настраиваемое DNS-имя с веб-приложением, его уровень [плана службы приложений](https://azure.microsoft.com/pricing/details/app-service/) должен быть платным (**Общий**, **Базовый**, **Стандартный** или **Премиум**). На этом шаге следует убедиться, что приложение службы приложений находится в поддерживаемой ценовой категории.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Вход в Azure

Откройте [портал Azure](https://portal.azure.com) и войдите в систему, используя свою учетную запись Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Переход к приложению на портале Azure

В меню слева выберите **Службы приложений**, а затем щелкните имя своего приложения Azure.

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-custom-domain/select-app.png)

Откроется страница управления приложением службы приложений.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Проверка ценовой категории

В левой области навигации страницы приложения перейдите к разделу **Параметры** и выберите **Увеличить масштаб (план службы приложений)**.

![Меню увеличения масштаба](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Текущий уровень приложения выделен синей рамкой. Убедитесь, что приложение не находится в ценовой категории **Бесплатный**. Использование личного домена DNS не поддерживается на уровне **Бесплатный**. 

![Проверка ценовой категории](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Если план службы приложений не **бесплатный**, закройте страницу **Выбор ценовой категории** и перейдите к разделу [Сопоставление записи CNAME](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Изменение уровня плана службы приложений

Выберите любой из платных уровней (**Общий**, **Базовый**, **Стандартный** или **Премиум**). 

Нажмите кнопку **Выбрать**.

![Проверка ценовой категории](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Если вы увидите уведомление ниже, значит уровень плана службы приложений изменен.

![Подтверждение операции масштабирования](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Сопоставление записи CNAME

В примере из руководства вы добавите запись CNAME для поддомена `www` (например, `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Создание записи CNAME

Добавьте запись CNAME для сопоставления поддомена с именем узла по умолчанию приложения (`<app_name>.azurewebsites.net`, где `<app_name>` — это имя приложения).

Для примера домена `www.contoso.com` добавьте запись CNAME, которая сопоставляет имя `www` с `<app_name>.azurewebsites.net`.

После добавления этой записи страница управления записями DNS выглядит так:

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Включение сопоставления записи CNAME в приложении Azure

В левой области навигации страницы приложения на портале Azure выберите **Личные домены**. 

![Меню личных доменов](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

На странице **Личные домены** приложения добавьте в список полное настраиваемое DNS-имя (`www.contoso.com`).

Щелкните значок **+** рядом с параметром **Добавить имя узла**.

![Добавление имени узла](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Введите полное доменное имя, для которого вы добавили запись CNAME, например `www.contoso.com`. 

Выберите **Проверка**.

После этого активируется кнопка **Добавить имя узла**. 

Убедитесь, что в поле **Тип записи имени узла** выбрано значение **CNAME (www.example.com или любой поддомен)**.

Выберите **Добавить имя узла**.

![Добавление DNS-имени в приложение](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Возможно, потребуется некоторое время для того, чтобы новое имя узла отобразилось на странице **Личные домены** вашего приложения. Попробуйте обновить браузер, чтобы обновить данные.

![Запись CNAME добавлена](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Если вы пропустили шаг или где-то допустили опечатку, в нижней части страницы появится сообщение об ошибке проверки.

![Ошибка проверки](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Сопоставление записи A

В примере из руководства вы добавите запись A для корневого домена (например, `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Копирование IP-адреса приложения

Чтобы сопоставить запись A, необходим внешний IP-адрес вашего приложения. Этот IP-адрес можно найти на странице **Личные домены** приложения на портале Azure.

В левой области навигации страницы приложения на портале Azure выберите **Личные домены**. 

![Меню личных доменов](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

На странице **Личные домены** скопируйте IP-адрес приложения.

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Создание записи A

Чтобы сопоставить запись А с приложением, службе приложений требуется **две** записи DNS:

- Запись **A** для сопоставления с IP-адресом приложения.
- Запись типа **TXT** для сопоставления с именем узла по умолчанию приложения, `<app_name>.azurewebsites.net`. Служба приложений использует эту запись только во время настройки, чтобы убедиться, что вы являетесь владельцем личного домена. После проверки и настройки личного домена в службе приложений эту TXT-запись можно удалить. 

Для примера домена `contoso.com` создайте запись A и запись типа TXT в соответствии со следующей таблицей (`@` обычно представляет корневой домен). 

| Тип записи | Узел | Значение |
| - | - | - |
| A | `@` | IP-адрес из раздела [Копирование IP-адреса приложения](#info). |
| TXT | `@` | `<app_name>.azurewebsites.net` |

После добавления этих записей страница управления записями DNS выглядит так:

![Страница записей DNS](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Включение сопоставления записи A в приложении

На странице **Личные домены** приложения на портале Azure добавьте в список полное настраиваемое DNS-имя (например, `contoso.com`).

Щелкните значок **+** рядом с параметром **Добавить имя узла**.

![Добавление имени узла](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Введите полное доменное имя, для которого вы добавили запись A, например `contoso.com`.

Выберите **Проверка**.

После этого активируется кнопка **Добавить имя узла**. 

Убедитесь, что в поле **Тип записи имени узла** выбрано значение **Запись А (example.com)**.

Выберите **Добавить имя узла**.

![Добавление DNS-имени в приложение](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Возможно, потребуется некоторое время для того, чтобы новое имя узла отобразилось на странице **Личные домены** вашего приложения. Попробуйте обновить браузер, чтобы обновить данные.

![Запись добавлена](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Если вы пропустили шаг или где-то допустили опечатку, в нижней части страницы появится сообщение об ошибке проверки.

![Ошибка проверки](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Сопоставление домена с подстановочными знаками

В примере из руководства вы сопоставите [DNS-имя с подстановочными знаками](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (например, `*.contoso.com`) с приложением службы приложений, добавив запись CNAME. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Создание записи CNAME

Добавьте запись CNAME для сопоставления имени с подстановочными знаками с именем узла по умолчанию приложения (`<app_name>.azurewebsites.net`).

Для примера домена `*.contoso.com` запись CNAME сопоставит имя `*` с `<app_name>.azurewebsites.net`.

После добавления записи CNAME страница управления записями DNS выглядит так:

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Включение сопоставления записи CNAME в приложении

Теперь вы можете добавить любой дочерний домен, соответствующий имени с подстановочными знаками в приложении (например, `sub1.contoso.com` и `sub2.contoso.com` соответствует `*.contoso.com`). 

В левой области навигации страницы приложения на портале Azure выберите **Личные домены**. 

![Меню личных доменов](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Щелкните значок **+** рядом с параметром **Добавить имя узла**.

![Добавление имени узла](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Введите полное доменное имя, соответствующее домену с подстановочными знаками (например, `sub1.contoso.com`), а затем выберите **Проверка**.

После этого активируется кнопка **Добавить имя узла**. 

Убедитесь, что в поле **Тип записи имени узла** выбрано значение **CNAME (www.example.com или любой поддомен)**.

Выберите **Добавить имя узла**.

![Добавление DNS-имени в приложение](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Возможно, потребуется некоторое время для того, чтобы новое имя узла отобразилось на странице **Личные домены** вашего приложения. Попробуйте обновить браузер, чтобы обновить данные.

Щелкните значок **+**, чтобы добавить другое имя узла, соответствующее домену с подстановочными знаками. Например, добавьте `sub2.contoso.com`.

![Запись CNAME добавлена](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Тестирование в браузере

Перейдите к DNS-именам, настроенным ранее (например, `contoso.com`, `www.contoso.com`, `sub1.contoso.com` и `sub2.contoso.com`).

![Переход к приложению Azure на портале](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>Устраните ошибки 404 "Не удалось найти веб-сайт"

Если появляется ошибка HTTP 404 (не найдено) при переходе по URL-адресу вашего личного домена, убедитесь, что этот домен обеспечивает разрешение IP-адреса приложения, с помощью <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Если это не так, ошибка может быть вызвана одной из следующих причин.

- На настроенном личном домене отсутствует запись A и (или) запись CNAME.
- В кэше клиента браузера сохранен старый IP-адрес вашего домена. Выполните очистку кэша и еще раз проверьте разрешение имен DNS На компьютере Windows кэш можно очистить с помощью команды `ipconfig /flushdns`.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>Прямой URL-адрес по умолчанию для пользовательского каталога

По умолчанию служба приложений направляет веб-запросы в корневой каталог кода приложения. Однако некоторые веб-платформы не запускаются в корневом каталоге. Например, [Laravel](https://laravel.com/) запускается в подкаталоге `public`. Чтобы продолжить пример DNS `contoso.com`, такое приложение будет доступно по адресу `http://contoso.com/public`, но вместо этого вам потребуется направлять `http://contoso.com` в каталог `public`. Этот шаг включает в себя не разрешение имен DNS, а настройку виртуального каталога.

Для этого выберите **Параметры приложения** в левой области навигации на странице веб-приложения. 

В нижней части страницы корневой виртуальный каталог `/` указывает на `site\wwwroot` по умолчанию. Это корневой каталог кода вашего приложения. Измените его, например, указав `site\wwwroot\public`, и сохраните изменения. 

![Настройка виртуального каталога](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

По завершении операции приложение должно вернуть соответствующую страницу в корневом каталоге (например, http://contoso.com).

## <a name="automate-with-scripts"></a>Автоматизация с помощью сценариев

Управление личными доменами можно автоматизировать с помощью сценариев, воспользовавшись [Azure CLI](/cli/azure/install-azure-cli) или [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Инфраструктура CLI Azure 

Приведенная ниже команда добавляет заданное настраиваемое DNS-имя для приложения службы приложений. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

Дополнительные сведения см. в статье [Сопоставление пользовательского домена с веб-приложением](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

Приведенная ниже команда добавляет заданное настраиваемое DNS-имя для приложения службы приложений. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Дополнительные сведения см. в статье [Назначение пользовательского домена веб-приложению](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Сопоставление поддомена с помощью записи CNAME.
> * Сопоставление корневого домена с помощью записи A.
> * Сопоставление домена с подстановочным знаком с помощью записи CNAME.
> * Автоматизация сопоставления доменов с помощью скриптов.

Перейдите к следующему руководству, чтобы научиться привязывать пользовательский SSL-сертификат к веб-приложению.

> [!div class="nextstepaction"]
> [Привязывание существующего настраиваемого SSL-сертификата к веб-приложениям Azure](app-service-web-tutorial-custom-ssl.md)
