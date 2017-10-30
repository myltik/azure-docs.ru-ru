---
title: "Azure Active Directory B2C: динамическая настройка пользовательского интерфейса Azure AD B2C с помощью пользовательских политик"
description: "Сведения о том, как обеспечить поддержку нескольких фирменных символик с помощью содержимого HTML или каскадных таблиц стилей (CSS), которое динамически изменяется в среде выполнения."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C: настройка пользовательского интерфейса с динамическим содержимым, используя пользовательские политики
Пользовательские политики Azure AD B2C позволяют отправлять параметр в строке запроса. Этот параметр передается в конечную точку HTML и может динамически изменять содержимое страницы. Например, можно изменить фоновое изображение страницы регистрации или входа в B2C на основе параметра, передаваемого из веб-приложения или мобильного приложения. 

## <a name="prerequisites"></a>Предварительные требования
Эта статья посвящена настройке пользовательского интерфейса Azure AD B2C с помощью **динамического содержимого**, используя пользовательские политики. Чтобы приступить к настройке пользовательского интерфейса политики, ознакомьтесь со статьей [Azure Active Directory B2C. Настройка пользовательского интерфейса с помощью настраиваемой политики](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>
>  В статье [Azure Active Directory B2C. Настройка пользовательского интерфейса с помощью настраиваемой политики](active-directory-b2c-ui-customization-custom.md) представлены сведения об основных настройках пользовательского интерфейса Azure AD B2C:
> * информация о возможности настройки пользовательского интерфейса страницы;
> * информация о средстве, которое поможет протестировать возможности настройки пользовательского интерфейса страницы с помощью предоставленного образца содержимого;
> * описание основных элементов пользовательского интерфейса для каждого типа страницы;
> * рекомендации по использованию этих возможностей.

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>Добавление ссылки на шаблоны HTML5 и CSS к пути взаимодействия пользователя

В пользовательской политике определение содержимого определяет универсальный код ресурса (URI) страницы HTML5, используемой для данного шага пользовательского интерфейса. Например, страницы входа или регистрации. Базовая политика определяет внешний вид по умолчанию, указывая на универсальный код ресурса (URI) файлов HTML5 (ссылается на содержимое CSS). В политике расширения можно изменить внешний вид путем переопределения LoadUri для этого файла HTML5. Таким образом определения содержимого содержат URL-адреса внешнего содержимого, которое определяется путем создания файлов HTML5 или CSS соответствующим образом. 

Раздел `ContentDefinitions` содержит серии XML-элементов `ContentDefinition`. Атрибут идентификатора элемента `ContentDefinition` указывает тип страниц, относящихся к определению содержимого. То есть контекст, в котором будет использоваться пользовательский шаблон HTML5 или CSS. В следующей таблице описан набор идентификаторов определений содержимого, распознаваемых ядром IEF, и типы страниц, относящихся к ним.

| Идентификатор для определения содержимого | Шаблон HTML5 по умолчанию| Описание | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Страница ошибки.** Эта страница отображается при обнаружении исключения или ошибки. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Страница выбора поставщика удостоверений.** На этой странице содержится список поставщиков удостоверений, которых можно выбирать во время входа пользователя. Обычно это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Выбор поставщика удостоверений для регистрации.** На этой странице содержится список поставщиков удостоверений, которых можно выбирать во время регистрации. Это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница восстановления пароля.** Эта страница содержит форму, которую нужно заполнить для сброса пароля.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница входа в локальную учетную запись.** Эта страница содержит форму для входа в локальную учетную запись (на основе адреса электронной почты или имени пользователя). Форма может содержать текстовое поле ввода и окно ввода пароля. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница регистрации локальной учетной записи**. Эта страница содержит форму для регистрации локальной учетной записи (на основе адреса электронной почты или имени пользователя). Форма может содержать разные элементы управления для ввода текста, например: текстовое поле, поле ввода пароля, переключатель, раскрывающийся список с единственным выбором или флажки множественного выбора. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Страница Многофакторной идентификации.** Эта страница позволяет пользователю подтвердить свой номер телефона (с помощью SMS-сообщения или голосового вызова) во время регистрации или входа. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница регистрации учетной записи социальных сетей.** Эта страница содержит форму регистрации, которую нужно заполнить при регистрации с использованием имеющейся учетной записи от поставщиков удостоверений социальных сетей. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Страница обновления профиля.** Эта страница содержит форму для обновления профиля пользователя. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Единая страница регистрации или входа.** Эта страница обрабатывает регистрацию и вход пользователей. Пользователи могут использовать поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальные учетные записи.  |

## <a name="serving-dynamic-content"></a>Обслуживание динамического содержимого
В предыдущей статье [Azure Active Directory B2C. Настройка пользовательского интерфейса с помощью настраиваемой политики](active-directory-b2c-ui-customization-custom.md) выполнялась отправка файлов HTML5 в хранилище BLOB-объектов Azure. Эти файлы HTML5 являются статичными и отображают то же HTML-содержимое для каждого запроса. В этой статье используется веб-приложение ASP.NET, которое может принимать параметры строки запроса и реагировать соответствующим образом. В этом пошаговом руководстве описаны следующие операции:
* создание веб-приложения ASP.NET Core, на котором размещен шаблон HTML5; 
* добавление пользовательского шаблона HTML5 _unified.cshtml_; 
* публикация веб-приложения в службе приложений Azure; 
* настройка CORS для веб-приложения;
* переопределение элементов `LoadUri` для указания на файл HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Шаг 1. Создание веб-приложения ASP.NET

1.  Создайте проект в Visual Studio, последовательно выбрав пункты **Файл > Создать > Проект**.
2.  В диалоговом окне **Новый проект** последовательно выберите пункты **Visual C# > Интернет > Веб-приложение ASP.NET Core (.NET Core)**.
3.  Присвойте имя приложению, например Contoso.AADB2C.UI, а затем нажмите кнопку **ОК**.

    ![Создание проекта Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  Выберите шаблон **Веб-приложение**.
5.  Обязательно выберите **Без аутентификации** в параметрах проверки подлинности.

    ![Выбор шаблона веб-API](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  Нажмите кнопку **ОК**, чтобы создать проект.

## <a name="step-2-create-mvc-view"></a>Шаг 2. Создание представления MVC
### <a name="step-21-download-b2c-built-in-html5-template"></a>Шаг 2.1. Скачивание встроенного в B2C шаблона HTML5
Пользовательский шаблон HTML5 основан на встроенном в B2C шаблоне HTML5. Можно скачать файл [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) отдельно или из [начального пакета](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Этот файл HTML5 используется для единой страницы регистрации или входа в систему.

### <a name="step-22-add-mvc-view"></a>Шаг 2.2. Добавление представления MVC
1. Щелкните правой кнопкой мыши Views/Home, а затем **Добавить > Новый элемент**.
 ![Добавление нового элемента MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. В диалоговом окне **Добавление нового элемента — Contoso.AADB2C.UI** выберите **Интернет > ASP > NET**.
3. Нажмите **Страница представления MVC**.
4. В диалоговом окне **Имя** измените имя на _unified.cshtml_.
5. Щелкните **Добавить**
 ![Add MVC view](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png) (Добавить представление MVC).
6.  Если этот файл еще не открыт, дважды щелкните его. Очистите содержимое _unified.cshtml_.
7. В целях демонстрации мы удалим ссылку на страницу макета. Добавьте следующий фрагмент кода в _unified.cshtml_.

    ```C#
    @{
        Layout = null;
    }
    ```

8. Откройте файл _unified.cshtml_, скачанный из шаблона HTML5, встроенного в AAD B2C.
9. Найдите подписи `@` и замените их на `@@`.
10. Скопируйте содержимое файла и вставьте его ниже определения макета. Код должен выглядеть следующим образом: ![файл unified.cshtml после добавления HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>Шаг 2.3. Изменение фонового изображения
10. Найдите элемент `<img>` с идентификатором `background_background_image` и замените `src` на _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_ или любое другое необходимое изображение фона.
![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>Шаг 2.4. Добавление представления в контроллер MVC
Откройте **Controllers\HomeController.cs** и добавьте следующий метод. 
```C
public IActionResult unified()
{
    return View();
}
```
Этот код указывает, что метод должен использовать файл шаблона представления для отображения ответа в браузере. Так как имя файла шаблона представления явно не указано, MVC по умолчанию будет использовать файл представления _unified.cshtml_ в папке /Views/Home.

После добавления _единого_ метода код будет выглядеть следующим образом: ![Изменение контроллера для отображения представления](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png).

Выполните отладку веб-приложения и убедитесь, что _единая_ страница доступна. Например, `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>Шаг 2.5. Публикация в Azure
1.  В **обозревателе решений** щелкните правой кнопкой мыши проект **Contoso.AADB2C.UI** и выберите **Опубликовать**.

    ![Публикация в службе приложений Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  Выберите **Служба приложений Microsoft Azure** и нажмите кнопку **Опубликовать**.

    ![Создание службы приложений Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    С помощью диалогового окна **Создать службу приложений** можно создать все ресурсы Azure, необходимые для запуска веб-приложения ASP.NET в Azure.

> [!NOTE]
>
>Дополнительные сведения см. в статье [Создание веб-приложения ASP.NET в Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3.  В поле **Имя веб-приложения** введите уникальное имя (допустимые символы: `a-z`, `0-9` и `-`). URL-адрес веб-приложения: `http://<app_name>.azurewebsites.NET`, где `<app_name>` — имя веб-приложения. Можно принять имя, созданное автоматически, если оно является уникальным.

    Нажмите кнопку **Создать**, чтобы начать создавать ресурсы Azure.

    ![Предоставление свойств службы приложений](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  По завершении работы мастера веб-приложение ASP.NET будет опубликовано в Azure, запущено в браузере по умолчанию и будет скопирован URL-адрес _единой_ страницы. Например, _https://<имя_приложения>.azurewebsites.net/home/unified_.

## <a name="step-3-configure-cors-in-azure-app-service"></a>Шаг 3. Настройка CORS в службе приложений Azure
1. В браузере перейдите на [портал Azure](https://portal.azure.com/).

2. Щелкните **Службы приложений**и выберите имя приложения API.

    ![Выбор приложения API на портале](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. В разделе **Параметры** прокрутите вниз и найдите раздел **API**, а затем щелкните **CORS**.

    ![Выбор параметров CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. В текстовое поле введите URL-адреса, с которых следует разрешить вызовы JavaScript,
или введите символ звездочки (*). В таком случае будут приниматься вызовы из всех исходных доменов.

5. Щелкните **Сохранить**.

    ![Щелкните Сохранить](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

После нажатия кнопки "Сохранить" приложение API начнет принимать вызовы JavaScript с указанных URL-адресов. 

## <a name="step-4-html5-template-validation"></a>Шаг 4. Проверка шаблона HTML5
Шаблон HTML5 готов к использованию.  Однако он недоступен ни в одном из `ContentDefinition`. Прежде чем добавлять `ContentDefinition` для пользовательской политики, необходимо:
* Убедитесь, что содержимое HTML5 соответствует и доступно.
* Убедитесь, что на сервере содержимого включен механизм CORS.

>[!NOTE]
>
>Чтобы проверить, включен ли CORS на веб-сайте, на котором размещается содержимое, и протестировать запросы CORS, можно использовать веб-сайт http://test-cors.org/. 

* Убедитесь, что обслуживаемое содержимое защищено с помощью **HTTPS**.
* Используйте **абсолютные URL-адреса** для всех ссылок, а также содержимого и изображений CSS, например https://ваш_домен/content.

## <a name="step-5-configure-your-content-definition"></a>Шаг 5. Настройка определения содержимого
Чтобы настроить `ContentDefinition`:
1.  Откройте базовый файл политики (например, TrustFrameworkBase.xml).
2.  Найдите элемент `<ContentDefinitions>` и скопируйте все содержимое узла `<ContentDefinitions>`.
3.  Откройте файл расширения (например, TrustFrameworkExtensions.xml) и найдите элемент `<BuildingBlocks>`. Если элемент не существует, добавьте его.
4.  Вставьте весь скопированный узел `<ContentDefinitions>` как дочерний узел элемента `<BuildingBlocks>`. 
5.  Найдите узел `<ContentDefinition>`, содержащий `Id="api.signuporsignin"` в скопированном файле XML.
6.  Измените значение `LoadUri` из _~/tenant/default/unified_ на _https://имя_приложения.azurewebsites.net/home/unified_ Пользовательская политика должна выглядеть следующим образом: ![Определение содержимого](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Шаг 6. Отправка политики в клиент
1.  На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и откройте **Azure AD B2C**.
2.  Выберите **Инфраструктура процедур идентификации**.
3.  Откройте колонку **Все политики**.
4.  Щелкните **Отправить политику**.
5.  Установите флажок **Перезаписать политику, если она существует**.
6.  **Отправьте** файл TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Шаг 7. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1.  Откройте **Параметры Azure AD B2C** и перейдите к элементу **Инфраструктура процедур идентификации**.

    >[!NOTE]
    >
    >    Для использования команды **Запустить сейчас** необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. 
    >    Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).
    >

2.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**.
3.  Вы должны увидеть настраиваемый HTML5 с ранее созданным фоном: ![Политика регистрации или входа в систему](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>Шаг 8: Добавление динамического содержимого
В этом разделе мы изменили фон на основе запроса параметра строки с именем _campaignId_. Приложение, основанное на утверждениях (веб- или мобильное приложение), отправляет параметр в AAD B2C. Политика считывает параметр и отправляет его значение в шаблон HTML5. 


### <a name="step-81-adding-content-definition-parameter"></a>Шаг 8.1. Добавление параметра определения содержимого

Чтобы добавить элемент `ContentDefinitionParameters`:
1.  Откройте файл политики SignUpOrSignin (например, SignUpOrSignin.xml).
2.  Найдите узел `<UserJourneyBehaviors>`. 
4.  Добавьте следующий фрагмент XML-кода в `<UserJourneyBehaviors>`. 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>Шаг 8.2. Изменение кода так, чтобы принять параметр строки запроса и заменить фоновое изображение соответствующим образом
В этом разделе изменяется _единый_ метод HomeController для приема параметра campaignId. Затем метод проверяет значение и устанавливает соответствующее значение `ViewData["background"]`.

1. Откройте **Controllers\HomeController.cs** и измените метод `unified` с помощью следующего фрагмента кода:

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Найдите элемент `<img>` с идентификатором `background_background_image` и замените `src` на `@ViewData["background"]`.

    ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>Шаг 8.3. Отправка изменений и публикация политики
1. Публикация проекта Visual Studio в службе приложений Azure
2. Отправьте файл политики SignUporSignIn.xml в AAD B2C.
3.  Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны. Выберите **Запустить сейчас**. Вы должны увидеть то же фоновое изображение, что и раньше.
4. Скопируйте URL-адрес из адресной строки браузера.
5. Добавьте параметр запроса строки _campaignId_ в универсальный код ресурса (URI). Например, добавьте `&campaignId=hawaii`, как показано на следующем рисунке: ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. Нажмите **Ввод**, и на странице отобразится фон Гавайев: ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. Теперь измените значение на *Tokyo* и нажмите **Ввод**. На странице появится фон Токио: ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Шаг 9. Изменение остальной части пути взаимодействия пользователя
Если перейти на страницу входа и щелкнуть ссылку **Подписаться**, появится фон по умолчанию, а не заданный вами. Это происходит из-за того, что была изменена страница регистрации и входа, однако необходимо изменить также остальные определения содержимого с самостоятельным утверждением. Чтобы сделать это, выполните следующие действия.
* На шаге 2:
    * Скачайте файл **selfasserted**.
    * Скопируйте содержимое файла.
    * Создайте представление **selfasserted**.
    * Добавьте **selfasserted** в контроллер **Home**.
* На шаге 4: 
    * В политике расширения найдите узел `<ContentDefinition>`, который содержит `Id="api.selfasserted"`, `Id="api.localaccountsignup"` и `Id="api.localaccountpasswordreset"`.
    *  Задайте для атрибута `LoadUri` универсальный код ресурса (URI) с самостоятельным утверждением.
* На шаге 8.2 измените код, чтобы принимать параметры запроса строки. 
* Отправьте файл политики TrustFrameworkExtensions.xml и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.
* Выполните тест политики и щелкните **Подписаться**, чтобы просмотреть результат.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Скачивание полного текста файлов политики и кода (необязательно)
* Мы советуем создать свой сценарий и собственные файлы пользовательской политики, а не использовать эти примеры файлов. Для этого вам будет полезно изучить пошаговое руководство по началу работы с пользовательскими политиками.  [Примеры файлов политики для справки](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* Вы можете загрузить полный код примера для решения Visual Studio [отсюда](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




