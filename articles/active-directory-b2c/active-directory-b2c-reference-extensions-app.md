---
title: "Приложения расширений Azure Active Directory B2C | Документация Майкрософт"
description: "Восстановление приложения b2c-extensions-app"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: f0392e32-0771-473c-a799-81438ca2bcff
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/17/2017
ms.author: parja
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 17500b572a0e92c1c233c6967840a5b6d96e21cb
ms.contentlocale: ru-ru
ms.lasthandoff: 08/17/2017

---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: приложение расширений

После создания каталога Azure AD B2C в нем автоматически создается приложение `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`. Это приложение, **b2c-extensions-app**, отображается в разделе *Регистрация приложений*. Служба Azure AD B2C использует его для хранения информации о пользователях и пользовательских атрибутах. При удалении приложения Azure AD B2C и рабочая среда не будут правильно работать.

> [!IMPORTANT]
> Не удаляйте приложение b2c-extensions-app, если вы не планируете сразу же удалить клиент. Если приложение удалено более 30 дней назад, сведения о пользователе будут безвозвратно утеряны.

## <a name="verifying-that-the-extensions-app-is-present"></a>Проверка наличия приложения расширений

Чтобы проверить наличие приложения b2c-extensions-app, сделайте следующее:

1. В клиенте Azure AD B2C в меню навигации слева щелкните **Больше услуг**.
1. Найдите и откройте элемент **Регистрация приложений**.
1. Найдите приложение, имя которого начинается с **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Восстановление приложения расширений

Если вы случайно удалили приложение b2c-extensions-app, его можно восстановить в течение 30 дней. Восстановить приложение можно с помощью API Graph:

1. Перейдите по ссылке [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Войдите на сайт как глобальный администратор каталога Azure AD B2C, в котором необходимо восстановить удаленное приложение.
1. Выполните запрос HTTP GET для URL-адреса `https://graph.windows.net/{tenantName}.onmicrosoft.com/deletedApplications` с версией API 1.6. Обязательно замените `{tenantName}` именем своего клиента. При выполнении этой операции отображается список всех приложений, удаленных за последние 30 дней.
1. Найдите в списке приложение, имя которого начинается с b2c-extension-app, и скопируйте значение его свойства `objectid`.
1. Выполните запрос HTTP POST для URL-адреса `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Замените часть `{OBJECTID}` URL-адреса на `objectid` из предыдущего шага. 

Теперь [восстановленное приложение может появиться](#verifying-that-the-extensions-app-is-present) на портале Azure.

> [!NOTE]
> Приложение можно восстановить, только если оно было удалено менее 30 дней назад. Если прошло более 30 дней, данные будут утеряны. Для получения дополнительной помощи отправьте запрос в службу поддержки.
