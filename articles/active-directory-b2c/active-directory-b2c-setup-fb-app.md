---
title: 'Azure Active Directory B2C: настройка Facebook | Документация Майкрософт'
description: Обеспечьте регистрацию и вход для пользователей с учетными записями Facebook в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 8/7/2017
ms.author: davidmu
ms.openlocfilehash: 899677500b0d33b5f98807a341449199b6b3dcac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: регистрация и вход пользователей с учетными записями Facebook
## <a name="create-a-facebook-application"></a>Создание приложения Facebook
Чтобы использовать Facebook в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Facebook и задать в нем правильные параметры. Для этого потребуется учетная запись Facebook. Если у вас нет этой учетной записи, ее можно получить на сайте [https://www.facebook.com/](https://www.facebook.com/).

1. Перейдите на [веб-сайт разработчиков Facebook](https://developers.facebook.com/) и войдите с помощью учетной записи Facebook.
2. Если вы этого еще не сделали, необходимо зарегистрироваться в качестве разработчика Facebook. Для этого щелкните **Register** (Зарегистрироваться) в правом верхнем углу страницы, примите политики Facebook и завершите регистрацию.
3. Щелкните **Мои приложения**, а затем — **Добавить новое приложение**. 
4. В форме укажите значения в полях **Отображаемое название** и **Эл. адрес для связи**.
5. Нажмите кнопку **Создайте ID приложения**. Для этого может потребоваться принять условия политики платформы Facebook и пройти проверку безопасности в сети.
6. В левом столбце щелкните **Настройки**, затем выберите пункт **Основное**, если он не выбран.
7. Выберите **категорию**. 
8. Щелкните **+Добавить платформу** и выберите **Веб-сайт**.
   
    ![Facebook — настройки](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook — настройки — веб-сайт](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Введите `https://login.microsoftonline.com/` в поле **URL-адрес сайта** и щелкните **Сохранить изменения** в нижней части страницы.
   
    ![Facebook — URL-адрес сайта](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Скопируйте значение **App ID**(Идентификатор приложения). Нажмите кнопку **Show** (Показать) и скопируйте значение **App Secret** (Секрет приложения). Оба значения необходимы для настройки Facebook в качестве поставщика удостоверений для вашего клиента. **Секрет приложения** — это важные учетные данные безопасности.
   
    ![Facebook — идентификатор приложения и секрет приложения](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. В области навигации слева щелкните **+ Add Product** (+ Добавить продукт), а затем нажмите кнопку **Настройка** рядом с разделом **Facebook Login** (Вход в Facebook).
   
    ![Facebook — вход в Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Щелкните **Параметры** на правой панели навигации в разделе **Facebook Login** (Вход в Facebook).

    ![Facebook — параметры входа в Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. В разделе **Client OAuth Settings** (Параметры OAuth клиента) в поле **Valid OAuth redirect URIs** (Допустимые URI перенаправления OAuth) введите `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com). Нажмите кнопку **Save Changes** (Сохранить изменения) в нижней части страницы.
    
    ![Facebook — универсальный код ресурса (URI) перенаправления OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Чтобы подготовить приложение Facebook к использованию Azure AD B2C, необходимо сделать его общедоступным. Для этого можно щелкнуть **App Review** (Просмотр приложения) в области навигации слева и выбрать для переключателя в верхней части страницы значение **Yes** (Да). Затем следует щелкнуть **Confirm** (Подтвердить).
    
    ![Facebook — публикация приложения](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Настройка Facebook в качестве поставщика удостоверений для клиента
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Facebook.
5. Щелкните **Identity provider type** (Тип поставщика удостоверений), выберите **Facebook** и нажмите кнопку **OK**.
6. Щелкните **Set up this identity provider** (Настроить этот поставщик удостоверений) и введите идентификатор и секрет приложения (ранее созданного приложения Facebook) в поля **Идентификатор клиента** и **Секрет клиента** соответственно.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Facebook.

> [!NOTE]
> При добавлении **поставщика удостоверений** в клиент имеющиеся политики не изменяются. Не забудьте обновить политики, добавив созданный поставщик удостоверений.
>