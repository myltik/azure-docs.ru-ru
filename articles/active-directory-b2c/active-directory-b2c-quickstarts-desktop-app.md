---
title: "Тестовый выпуск классического приложения Azure AD B2C | Документация Майкрософт"
description: "Протестируйте вход, регистрацию, изменение профиля и сброс пароля с помощью тестовой среды Azure AD B2C"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: mtillman
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 51f5643f0bd975beb939c2d5a8853810fb609ec9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>Тестовый выпуск классического приложения, настроенного с помощью Azure AD B2C

Azure Active Directory B2C обеспечивает управление идентификаторами облака для защиты приложения, бизнеса и клиентов.  В этом кратком руководстве используется образец классического приложения Windows Presentation Foundation (WPF), чтобы продемонстрировать следующее:

* Использование политики **Регистрация или вход** для создания учетной записи социальной сети или входа с помощью поставщика удостоверений в социальных сетях или локальной учетной записи, используя адрес электронной почты. 
* **Вызов API** для извлечения отображаемого имени из защищенного ресурса Azure AD B2C.

## <a name="prerequisites"></a>предварительным требованиям

* Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/) с указанными ниже рабочими нагрузками:
    - **Разработка классического приложения .NET**.

* Учетная запись социальных сетей Facebook, Google, Microsoft или Twitter. При отсутствии учетной записи социальных сетей требуется допустимый адрес электронной почты.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Скачивание примера приложения

[Скачайте или клонируйте образец приложения](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) из GitHub.

## <a name="run-the-app-in-visual-studio"></a>Запуск приложения в Visual Studio

В папке проекта образца приложения откройте решение `active-directory-b2c-wpf.sln` в Visual Studio. 

Выберите **Debug > Start Debugging** (Отладка > Начать отладку), чтобы выполнить сборку и запустить приложение. 

## <a name="create-an-account"></a>Создание учетной записи

Щелкните **Вход**, чтобы запустить рабочий процесс **регистрации или входа**. При создании учетной записи можно использовать имеющуюся учетную запись поставщика удостоверений в социальных сетях или учетную запись электронной почты.

![Пример приложения](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Регистрация с помощью поставщика удостоверений в социальных сетях

Чтобы зарегистрироваться с помощью поставщика удостоверений в социальных сетях, нажмите кнопку поставщика удостоверений, которого нужно использовать. Если вы предпочитаете использовать адрес электронной почты, перейдите к разделу [Регистрация с помощью адреса электронной почты](#sign-up-using-an-email-address).

![Поставщик входа или регистрации](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

В этом случае необходимо выполнить аутентификацию (вход) с помощью данных учетной записи социальных сетей и авторизовать приложение для чтения информации из учетной записи социальных сетей. Предоставляя доступ, приложение может получить сведения о профиле из учетной записи социальных сетей (например, имя и город). 

![Аутентификация и авторизация с помощью учетной записи социальных сетей](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

Сведения о профиле новой учетной записи будут заполнены информацией из учетной записи социальных сетей. Если необходимо, измените сведения и щелкните **Продолжить**.

![Сведения о новом профиле регистрации учетной записи](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

Учетная запись пользователя Azure AD B2C, использующая поставщик удостоверений, успешно создана. После входа в систему маркер доступа отображается в текстовом поле *Token info* (Сведения о маркере). Маркер доступа используется при получении доступа к ресурсу API.

![Маркер авторизации](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

Далее перейдите к разделу [Изменение профиля](#edit-your-profile).

### <a name="sign-up-using-an-email-address"></a>Регистрация с помощью адреса электронной почты

Если вы решили не использовать учетную запись социальных сетей для аутентификации, можно создать учетную запись пользователя Azure AD B2C, используя адрес электронной почты. Учетная запись локального пользователя Azure AD B2C использует Azure Active Directory в качестве поставщика удостоверений. Чтобы использовать свой адрес электронной почты, щелкните ссылку **Нет учетной записи?** Зарегистрируйтесь сейчас.

![Вход или регистрация с помощью электронной почты](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

Введите допустимый адрес электронной почты и нажмите кнопку **Отправить проверочный код**. Для получения проверочного кода из Azure AD B2C необходим допустимый адрес электронной почты.

Введите проверочный код, полученный по электронной почте, и нажмите кнопку **Проверить код**.

Введите данные профиля и нажмите кнопку **Создать**.

![Регистрация новой учетной записи с использованием адреса электронной почты](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

Новая учетная запись локального пользователя Azure AD B2C успешно создана. После входа в систему маркер доступа отображается в текстовом поле *Token info* (Сведения о маркере). Маркер доступа используется при получении доступа к ресурсу API.

![Маркер авторизации](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>Изменение профиля

Azure Active Directory B2C предоставляет функциональные возможности, обеспечивающие пользователям возможность обновления профилей. Щелкните **Изменить профиль**, чтобы изменить созданный профиль.

![Изменение профиля](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

Выберите поставщик удостоверений, связанный с созданной учетной записью. Например, если при создании учетной записи в качестве поставщика удостоверений использовался Twitter, выберите Twitter, чтобы изменить сведения связанного профиля.

![Выбор поставщика, связанного с профилем, который необходимо изменить](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

Измените **отображаемое имя** или **город**. 

![Обновить профиль](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

Новый маркер доступа появится в текстовом поле *Token info* (Сведения о маркере). Если вы хотите проверить изменения для профиля, скопируйте и вставьте маркер доступа в декодер маркеров https://jwt.ms.

![Маркер авторизации](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>Получение доступа к ресурсу

Щелкните **Call API** (Вызов API), чтобы выполнить запрос к защищенному ресурсу Azure AD B2C https://fabrikamb2chello.azurewebsites.net/hello. 

![Вызов API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

Приложение включает в себя маркер доступа, отображаемый при запросе в текстовом поле *Token info* (Сведения о маркере). API отправляет отображаемое имя, содержащееся в маркере доступа.

## <a name="next-steps"></a>Дополнительная информация

Следующим шагом является создание клиента Azure AD B2C и настройка образца для запуска с помощью клиента. 

> [!div class="nextstepaction"]
> [Создание клиента Azure Active Directory B2C на портале Azure](active-directory-b2c-get-started.md)
