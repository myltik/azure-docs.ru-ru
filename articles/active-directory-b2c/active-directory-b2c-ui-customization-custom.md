---
title: 'Azure AD B2C: настройка пользовательского интерфейса с помощью настраиваемых политик | Документация Майкрософт'
description: Сведения о настройке пользовательского интерфейса с помощью настраиваемых политик в Azure AD B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 41b756855ea91db291f4fdb5a963993de4b9cd7e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200864"
---
# <a name="azure-active-directory-b2c-configure-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C. Настройка пользовательского интерфейса с помощью настраиваемой политики

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Выполнив инструкции из этой статьи, вы получите настраиваемую политику регистрации и входа с фирменной символикой и оформлением. Благодаря Azure Active Directory B2C (Azure AD B2C) вы получаете практически полный контроль над содержимым HTML и CSS, которое представляется пользователям. При использовании настраиваемой политики вы настраиваете пользовательский интерфейс в XML-файле, а не с помощью элементов управления на портале Azure. 

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем продолжить, ознакомьтесь со статьей [Azure Active Directory B2C. Приступая к работе с настраиваемыми политиками](active-directory-b2c-get-started-custom.md). Для регистрации и входа с использованием локальных учетных записей необходимо иметь рабочую настраиваемую политику.

## <a name="page-ui-customization"></a>Настройка пользовательского интерфейса страницы

С помощью функции настройки пользовательского интерфейса страницы можно настроить оформление любой настраиваемой политики. Кроме того, можно обеспечить согласованность фирменной символики и визуализации в приложении и службе Azure AD B2C.

Вот как это работает. Служба Azure AD B2C выполняет код в браузере пользователя и использует современный метод — [общий доступ к ресурсам независимо от источника (CORS)](http://www.w3.org/TR/cors/). Сначала необходимо указать URL-адрес в настраиваемой политике с настраиваемым содержимым HTML. Azure AD B2C объединяет элементы пользовательского интерфейса с содержимым HTML, загруженным по указанному URL-адресу, и отображает страницу для пользователя.

## <a name="create-your-html5-content"></a>Создание содержимого HTML5

Создадим содержимое HTML с названием вашей торговой марки в заголовке.

1. Скопируйте следующий фрагмент кода HTML. Это код HTML5 с правильным форматом. Он содержит пустой элемент *\<div id="api"\>\</div\>*, заключенный в теги *\<body\>*. Этот элемент определяет расположение, в которое будет вставлено содержимое Azure AD B2C.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

   >[!NOTE]
   >По соображениям безопасности использование JavaScript для настройки сейчас заблокировано.

2. Вставьте скопированный фрагмент кода в текстовый редактор и сохраните файл как *customize-ui.html*.

## <a name="create-an-azure-blob-storage-account"></a>Создание учетной записи хранения BLOB-объектов Azure

>[!NOTE]
> В этой статье мы используем хранилище BLOB-объектов Azure для размещения содержимого. Можно выбрать для этого веб-сервер, но тогда нужно [включить для веб-сервера поддержку CORS](https://enable-cors.org/server.html).

Чтобы разместить HTML-содержимое в хранилище BLOB-объектов, сделайте следующее.

1. Войдите на [портале Azure](https://portal.azure.com).
2. В меню **Концентратор** выберите **Создать** > **Хранилище** > **Учетная запись хранения**.
3. Введите уникальное **имя** учетной записи хранения.
4. Для параметра **Модель развертывания** можно оставить значение **Диспетчер ресурсов**.
5. Измените **тип учетной записи** на **хранилище BLOB-объектов**.
6. Для параметра **Производительность** можно оставить значение **Стандартная**.
7. Для параметра **Репликация** можно оставить значение **RA-GRS**.
8. Для параметра **Уровень доступа** можно оставить значение **Горячий**.
9. Для параметра **Шифрование службы хранилища** можно оставить значение **Отключено**.
10. Выберите **подписку** для своей учетной записи хранения.
11. Создайте новую **группу ресурсов** или выберите существующую.
12. Выберите **географический регион** для учетной записи хранения.
13. Щелкните **Создать** , чтобы создать учетную запись хранения.  
    После развертывания колонка **Учетная запись хранения** откроется автоматически.

## <a name="create-a-container"></a>Создание контейнера

Чтобы создать общедоступный контейнер в хранилище BLOB-объектов, сделайте следующее.

1. Щелкните вкладку **Обзор**.
2. Щелкните **Контейнер**.
3. В поле **Имя** введите **$root**.
4. Задайте для параметра **Тип доступа** значение **BLOB-объект**.
5. Щелкните **$root**, чтобы открыть новый контейнер.
6. Щелкните **Отправить**.
7. Щелкните значок папки рядом с элементом **Выбор файла**.
8. Перейдите к файлу **customize-ui.html**, созданному ранее в разделе [Настройка пользовательского интерфейса страницы](#the-page-ui-customization-feature).
9. Щелкните **Отправить**.
10. Выберите BLOB-объект customize-ui.html, который вы загрузили.
11. Рядом с полем **URL-адрес** нажмите **Копировать**.
12. Вставьте скопированный URL-адрес в строку браузера и перейдите на сайт. Если он недоступен, убедитесь, что для типа доступа к контейнеру указано значение **BLOB-объект**.

## <a name="configure-cors"></a>Настройка CORS

Настройте хранилище BLOB-объектов для общего доступа к ресурсам независимо от источника (CORS), сделав следующее.

>[!NOTE]
>Хотите протестировать функцию настройки пользовательского интерфейса с помощью примера содержимого HTML и CSS? Используйте [простое вспомогательное приложение](active-directory-b2c-reference-ui-customization-helper-tool.md), чтобы отправить и настроить пример содержимого в учетной записи хранилища BLOB-объектов. Если вы используете такое приложение, перейдите к разделу об [изменении настраиваемой политики регистрации или входа](#modify-your-sign-up-or-sign-in-custom-policy).

1. В колонке **Хранилище** в разделе **Параметры** откройте **CORS**.
2. Щелкните **Добавить**.
3. Для параметра **Разрешенные источники** введите звездочку (\*).
4. В раскрывающемся списке **Разрешенные команды** выберите **GET** и **OPTIONS**.
5. Для параметра **Разрешенные заголовки** введите звездочку (\*).
6. Для параметра **Доступные заголовки** введите звездочку (\*).
7. Для параметра **Максимальная длительность (в секундах)** введите **200**.
8. Щелкните **Добавить**.

## <a name="test-cors"></a>Тестирование CORS

Проверьте готовность, сделав следующее.

1. Перейдите на веб-сайт [www.test-cors.org](http://www.test-cors.org/) и вставьте URL-адрес в поле **Remote URL** (Удаленный URL-адрес).
2. Щелкните **Send Request** (Отправить запрос).  
    Если произошла ошибка, проверьте правильность [параметров CORS](#configure-cors). Кроме того, вам может потребоваться очистить кэш браузера или открыть закрытый сеанс, нажав CTRL+SHIFT+P.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Изменение настраиваемой политики регистрации или входа

Под тегом *\<TrustFrameworkPolicy\>* верхнего уровня найдите теги *\<BuildingBlocks\>*. Между тегами *\<BuildingBlocks\>* добавьте тег *\<ContentDefinitions\>*, скопировав пример ниже. Замените свойство *your_storage_account* именем своей учетной записи хранения.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0</DataUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Отправка обновленной настраиваемой политики

1. На [портале Azure](https://portal.azure.com) [переключитесь в контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте колонку **Azure AD B2C**.
2. Щелкните **Все политики**.
3. Выберите **Отправить политику**.
4. Отправьте `SignUpOrSignin.xml` с добавленным ранее тегом *\<ContentDefinitions\>*.

## <a name="test-the-custom-policy-by-using-run-now"></a>Тестирование настраиваемой политики с помощью параметра **Запустить сейчас**

1. В колонке **Azure AD B2C** выберите **Все политики**.
2. Выберите отправленную вами настраиваемую политику и нажмите кнопку **Запустить сейчас**.
3. Вы сможете зарегистрироваться, используя адрес электронной почты.

## <a name="reference"></a>Справочные материалы

Здесь можно найти примеры шаблонов для настройки пользовательского интерфейса:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Папка sample_templates/wingtip содержит следующие файлы HTML:

| Шаблон HTML5 | ОПИСАНИЕ |
|----------------|-------------|
| *phonefactor.html* | Этот файл используется как шаблон для страницы многофакторной проверки подлинности. |
| *ResetPassword.html* | Этот файл используется как шаблон для страницы восстановления пароля. |
| *selfasserted.html* | Этот файл используется как шаблон для страницы регистрации учетных записей социальных сетей, страницы регистрации локальной учетной записи или страницы входа в локальную учетную запись. |
| *unified.html* | Этот файл используется как шаблон для единой страницы регистрации или входа в систему. |
| *updateprofile.html* | Этот файл используется как шаблон для страницы обновления профиля. |

В разделе [Изменение настраиваемой политики регистрации или входа](#modify-your-sign-up-or-sign-in-custom-policy) мы настроили определение содержимого для `api.idpselections`. Полный набор идентификаторов для определения содержимого, распознаваемых в инфраструктуре Identity Experience Framework Azure AD B2C, а также их описания представлены в таблице ниже.

| Идентификатор для определения содержимого | ОПИСАНИЕ | 
|-----------------------|-------------|
| *api.error* | **Страница ошибки.** Эта страница отображается при обнаружении исключения или ошибки. |
| *api.idpselections* | **Страница выбора поставщика удостоверений.** На этой странице содержится список поставщиков удостоверений, которых можно выбирать во время входа пользователя. Это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| *api.idpselections.signup* | **Выбор поставщика удостоверений для регистрации.** На этой странице содержится список поставщиков удостоверений, которых можно выбирать во время регистрации. Это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| *api.localaccountpasswordreset* | **Страница восстановления пароля.** Эта страница содержит форму, которую нужно заполнить для сброса пароля.  |
| *api.localaccountsignin* | **Страница входа в локальную учетную запись.** Эта страница содержит форму для входа в локальную учетную запись (на основе адреса электронной почты или имени пользователя). Форма может содержать текстовое поле ввода и окно ввода пароля. |
| *api.localaccountsignup* | **Страница регистрации локальной учетной записи.** Эта страница содержит форму для регистрации локальной учетной записи (на основе адреса электронной почты или имени пользователя). Форма может содержать разные элементы управления для ввода текста, например: текстовое поле, поле ввода пароля, переключатель, раскрывающийся список с единственным выбором или флажки множественного выбора. |
| *api.phonefactor* | **Страница Многофакторной идентификации.** Эта страница позволяет пользователю подтвердить свой номер телефона (с помощью SMS-сообщения или голосового вызова) во время регистрации или входа. |
| *api.selfasserted* | **Страница регистрации учетной записи социальных сетей.** Эта страница содержит форму регистрации, которую нужно заполнить при регистрации с использованием существующей учетной записи от таких поставщиков удостоверений социальных сетей, как Facebook или Google+. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| *api.selfasserted.profileupdate* | **Страница обновления профиля.** Эта страница содержит форму для обновления профиля пользователя. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| *api.signuporsignin* | **Единая страница регистрации или входа.** На этой странице обрабатываются данные для регистрации и входа пользователей, которые могут использовать поставщики удостоверений организаций, поставщики удостоверений социальных сетей, включая Facebook или Google+, или локальные учетные записи.  |

## <a name="next-steps"></a>Дополнительная информация

См. [справочное руководство по настройке пользовательского интерфейса для встроенных политик](active-directory-b2c-reference-ui-customization.md).
