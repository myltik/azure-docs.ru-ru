---
title: 'Azure Active Directory B2C: динамическая настройка пользовательского интерфейса Azure AD B2C с помощью пользовательских политик'
description: Сведения о том, как обеспечить поддержку нескольких фирменных символик с помощью содержимого HTML5 или каскадных таблиц стилей (CSS), которое динамически изменяется в среде выполнения.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/20/2017
ms.author: davidmu
ms.openlocfilehash: 77f6ae1df8a364eecc5e7d6d6fe3a07dd215ac16
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: настройка пользовательского интерфейса с динамическим содержимым, используя пользовательские политики

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Пользовательские политики Azure Active Directory B2C (Azure AD B2C) позволяют отправлять параметр в строке запроса. Передавая параметр в конечную точку HTML, вы можете динамически изменять содержимое страницы. Например, можно изменить фоновое изображение страницы регистрации или входа в Azure AD B2C на основе параметра, передаваемого из веб-приложения или мобильного приложения. 

## <a name="prerequisites"></a>предварительным требованиям
Эта статья посвящена настройке пользовательского интерфейса Azure AD B2C с помощью *динамического содержимого*, используя пользовательские политики. Сведения по началу работы см. в статье [Azure Active Directory B2C. Настройка пользовательского интерфейса с помощью настраиваемой политики](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>В статье об Azure AD B2C [Настройка пользовательского интерфейса с помощью настраиваемой политики](active-directory-b2c-ui-customization-custom.md) содержатся следующие основные сведения:
> * информация о возможности настройки пользовательского интерфейса страницы;
> * информация об основных средствах, которые помогут протестировать возможности настройки пользовательского интерфейса страницы с помощью предоставленного образца содержимого;
> * описание основных элементов пользовательского интерфейса для каждого типа страницы;
> * рекомендации по применению этой возможности.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Добавление ссылки на шаблоны HTML5 и CSS к пути взаимодействия пользователя

В пользовательской политике определение содержимого определяет универсальный код ресурса (URI) страницы HTML5, используемой для данного шага пользовательского интерфейса (например, страницы регистрации или входа). Базовая политика определяет внешний вид по умолчанию, указывая на URI файлов HTML5 (в содержимом CSS). В политике расширения можно изменить внешний вид путем переопределения LoadUri для этого файла HTML5. Определения содержимого содержат URL-адреса внешнего содержимого, которое определяется путем создания файлов HTML5 или CSS соответственно. 

Раздел `ContentDefinitions` содержит серии XML-элементов `ContentDefinition`. Атрибут идентификатора элемента `ContentDefinition` указывает тип страниц, относящихся к определению содержимого. Таким образом, этот элемент определяет контекст, который пользовательский шаблон HTML5/CSS будет применять. В следующей таблице описан набор идентификаторов определений содержимого, распознаваемых ядром IEF, и типы страниц, относящихся к ним.

| Идентификатор для определения содержимого | Шаблон HTML5 по умолчанию| ОПИСАНИЕ | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Страница ошибки.** Эта страница отображается при обнаружении исключения или ошибки. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Страница выбора поставщика удостоверений.** На этой странице содержится список поставщиков удостоверений, которые пользователи могут выбирать во время входа. Обычно это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Выбор поставщика удостоверений для регистрации.** На этой странице содержится список поставщиков удостоверений, которые пользователи могут выбирать во время регистрации. Это поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальных учетных записей. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница восстановления пароля.** Эта страница содержит форму, которую пользователи должны заполнить для сброса пароля.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница входа в локальную учетную запись.** Эта страница содержит форму для входа в локальную учетную запись (на основе адреса электронной почты или имени пользователя). Форма может содержать текстовое поле ввода и окно ввода пароля. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница регистрации локальной учетной записи**. Эта страница содержит форму для регистрации локальной учетной записи (на основе адреса электронной почты или имени пользователя). Форма может содержать разные элементы управления для ввода текста, например: текстовое поле, поле ввода пароля, переключатель, раскрывающийся список с единственным выбором или флажки множественного выбора. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Страница Многофакторной идентификации.** Эта страница позволяет пользователю подтвердить свой номер телефона (с помощью SMS-сообщения или голосового вызова) во время регистрации или входа. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Страница регистрации учетной записи социальных сетей.** Эта страница содержит форму, которую нужно заполнить при регистрации с использованием имеющейся учетной записи от поставщика удостоверений социальных сетей. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Страница обновления профиля.** Эта страница содержит форму для обновления профиля пользователя. Эта страница аналогична странице регистрации локальных учетных записей, за исключением полей для ввода пароля. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Единая страница регистрации или входа.** Эта страница обрабатывает регистрацию и вход пользователей. Пользователи могут использовать поставщики удостоверений организаций, социальных сетей, включая Facebook и Google+, или локальные учетные записи.  |

## <a name="serving-dynamic-content"></a>Обслуживание динамического содержимого
В статье [Azure Active Directory B2C. Настройка пользовательского интерфейса с помощью настраиваемой политики](active-directory-b2c-ui-customization-custom.md) выполнялась отправка файлов HTML5 в хранилище BLOB-объектов Azure. Эти файлы HTML5 являются статичными и отображают то же HTML-содержимое для каждого запроса. 

В этой статье используется веб-приложение ASP.NET, которое может принимать параметры строки запроса и реагировать соответствующим образом. 

В этом пошаговом руководстве описаны следующие операции:
* создание веб-приложения ASP.NET Core, где размещены шаблоны HTML5; 
* добавление пользовательского шаблона HTML5 _unified.cshtml_; 
* публикация веб-приложения в службе приложений Azure; 
* настройка общего доступа к ресурсам независимо от источника (CORS) для своего веб-приложения;
* переопределение элементов `LoadUri` для указания на файл HTML5.

## <a name="step-1-create-an-aspnet-web-app"></a>Шаг 1. Создание веб-приложения ASP.NET

1. Создайте проект в Visual Studio, выбрав **Файл** > **Создать** > **Проект**.

2. В окне **Новый проект** выберите **Visual C#** > **Интернет** > **Веб-приложение ASP.NET Core (.NET Core)**.

3. Присвойте имя приложению, например *Contoso.AADB2C.UI*, а затем нажмите кнопку **ОК**.

    ![Создание проекта Visual Studio](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Выберите шаблон **Веб-приложение**.

5. В параметрах проверки подлинности выберите вариант **Без аутентификации**.

    ![Выбор шаблона "Веб-приложение"](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Нажмите кнопку **ОК**, чтобы создать проект.

## <a name="step-2-create-mvc-view"></a>Шаг 2. Создание представления MVC
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>Шаг 2.1. Скачивание встроенного в B2C шаблона HTML5
Пользовательский шаблон HTML5 основан на встроенном в Azure AD B2C шаблоне HTML5. Можно скачать файл [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) отдельно или скачать шаблон из [начального пакета](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Этот файл HTML5 используется для создания единой страницы регистрации или входа в систему.

### <a name="step-22-add-the-mvc-view"></a>Шаг 2.2. Добавление представления MVC
1. Щелкните правой кнопкой мыши папку Views/Home, а затем выберите **Добавить** > **Новый элемент**.

    ![Добавление нового элемента MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. В окне **Добавление нового элемента — Contoso.AADB2C.UI** выберите **Интернет > ASP.NET**.

3. Выберите **Страница представления MVC**.

4. В диалоговом окне **Имя** измените имя на **unified.cshtml**.

5. Выберите **Добавить**.

    ![Добавление представления MVC](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Если файл *unified.cshtml* еще не открыт, дважды щелкните его, чтобы открыть, и очистите его содержимое.

7. В целях этого пошагового руководства мы удалим ссылку на страницу макета. Добавьте в файл _unified.cshtml_ следующий фрагмент кода:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Откройте файл _unified.cshtml_, скачанный из шаблона HTML5, встроенного в Azure AD B2C.

9. Замените одиночные символы @ двойными символами (@@).

10. Скопируйте содержимое файла и вставьте его ниже определения макета. Код должен выглядеть следующим образом:

    ![файл unified.cshtml после добавления содержимого HTML5](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>Шаг 2.3. Изменение фонового изображения

Найдите элемент `<img>` со значением `ID` *background_background_image* и замените `src` на **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** или любое другое необходимое изображение фона.

![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>Шаг 2.4. Добавление представления в контроллер MVC

1. Откройте **Controllers\HomeController.cs** и добавьте следующий метод: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Этот код указывает, что метод должен использовать файл шаблона *представления* для отображения ответа в браузере. Так как имя файла шаблона *представления* явно не указано, MVC по умолчанию будет использовать файл представления _unified.cshtml_ в папке */Views/Home*.
    
    После добавления метода _unified_ код должен выглядеть так:
    
    ![Изменение контроллера для отображения представления](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Выполните отладку веб-приложения и убедитесь, что _единая_ страница доступна (например, `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>Шаг 2.5. Публикация в Azure
1. В **обозревателе решений** щелкните правой кнопкой мыши проект **Contoso.AADB2C.UI**, а затем выберите **Опубликовать**.

    ![Публикация в службе приложений Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Выберите плитку **Служба приложений Microsoft Azure** и нажмите кнопку **Опубликовать**.

    ![Создание службы приложений Microsoft Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Откроется окно **Создать службу приложений**. В этом окне вы можете приступить к созданию всех необходимых ресурсов Azure для запуска веб-приложения ASP.NET в Azure.

    > [!NOTE]
    > Дополнительные сведения см. в разделе о публикации статьи [Создание веб-приложения ASP.NET в Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. В поле **Имя веб-приложения** введите уникальное имя (допустимые символы: a–z, A–Z, 0–9 и дефис (-)). URL-адрес веб-приложения: `http://<app_name>.azurewebsites.NET`, где `<app_name>` — имя веб-приложения. Можно принять имя, созданное автоматически, если оно является уникальным.

4. Нажмите кнопку **Создать**, чтобы начать создавать ресурсы Azure.

    ![Предоставление свойств службы приложений](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    По завершении процесса создания мастер опубликует веб-приложение ASP.NET в Azure и запустит его в браузере по умолчанию.

5. Скопируйте URL-адрес _единой_ страницы, например _https://<имя_приложения>.azurewebsites.net/home/unified_.

## <a name="step-3-configure-cors-in-azure-app-service"></a>Шаг 3. Настройка CORS в службе приложений Azure
1. На [портале Azure](https://portal.azure.com/) выберите **Службы приложений**, а затем щелкните имя своего приложения API.

    ![Выбор приложения API на портале Azure](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. В разделе **Параметры** в области **API** выберите **CORS**.

    ![Выбор параметров CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. В окне **CORS** в поле **Разрешенные источники** выполните одно из следующих действий.

    * Введите URL-адреса, с которых следует разрешить вызовы JavaScript.
    * Введите символ звездочки (*). В таком случае будут приниматься вызовы из всех исходных доменов.

4. Щелкните **Сохранить**.

    ![Окно CORS](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    После нажатия кнопки **Сохранить** приложение API начнет принимать вызовы JavaScript с указанных URL-адресов. 

## <a name="step-4-html5-template-validation"></a>Шаг 4. Проверка шаблона HTML5
Шаблон HTML5 готов к использованию. Однако он недоступен в коде `ContentDefinition`. Прежде чем добавить `ContentDefinition` в пользовательскую политику, убедитесь в следующем.
* Содержимое HTML5 соответствует и доступно.
* На сервере содержимого включен механизм CORS.

    >[!NOTE]
    >Чтобы проверить, что на сайте, где вы размещаете содержимое, включен механизм CORS и можно тестировать запросы CORS, перейдите на веб-сайт [test-cors.org](http://test-cors.org/). 

* Обслуживаемое содержимое защищено с помощью **HTTPS**.
* Вы используете *абсолютные URL-адреса*, например *https://yourdomain/content*, для всех ссылок, содержимого CSS и изображений.

## <a name="step-5-configure-your-content-definition"></a>Шаг 5. Настройка определения содержимого
Чтобы настроить `ContentDefinition`, сделайте следующее:
1. Откройте базовый файл политики (например, *TrustFrameworkBase.xml*).

2. Найдите элемент `<ContentDefinitions>`, а затем скопируйте все содержимое узла `<ContentDefinitions>`.

3. Откройте файл расширения (например, *TrustFrameworkExtensions.xml*) и найдите элемент `<BuildingBlocks>`. Если такой элемент не существует, добавьте его.

4. Вставьте весь скопированный узел `<ContentDefinitions>` как дочерний узел элемента `<BuildingBlocks>`. 

5. Найдите узел `<ContentDefinition>`, содержащий `Id="api.signuporsignin"` в скопированном файле XML.

6. Измените значение `LoadUri` с _~/tenant/default/unified_ на _https://<имя_приложения>.azurewebsites.net/home/unified_.  
    Пользовательская политика должна выглядеть следующим образом:
    
    ![Определение содержимого](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Шаг 6. Отправка политики в клиент
1. На [портале Azure](https://portal.azure.com) переключитесь в [контекст клиента Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) и выберите **Azure AD B2C**.

2. Выберите **Инфраструктура процедур идентификации**.

3. Выберите **Все политики**.

4. Щелкните **Отправить политику**.

5. Установите флажок **Перезаписать политику, если она существует**.

6. Отправьте файл *TrustFrameworkExtensions.xml* и немного подождите, чтобы удостовериться, что он прошел проверку.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Шаг 7. Тестирование настраиваемой политики с помощью команды "Запустить сейчас"
1. Откройте **параметры Azure AD B2C** и выберите **Identity Experience Framework**.

    >[!NOTE]
    >Для использования команды Run now (Запустить сейчас) необходимо, чтобы в клиенте было предварительно зарегистрировано хотя бы одно приложение. Дополнительные сведения о регистрации приложений см. в статье [Azure AD B2C: начало работы](active-directory-b2c-get-started.md) или [Регистрация приложения](active-directory-b2c-app-registration.md).

2. Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны, а затем выберите **Run Now** (Запустить сейчас).  
    Вы должны увидеть настраиваемый HTML5 с ранее созданным фоном.

    ![Политика регистрации или входа в систему](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>Шаг 8. Добавление динамического содержимого
Измените фон на основе запроса параметра строки с именем _campaignId_. Приложение проверяющей стороны (веб- или мобильное приложение) отправляет параметр в Azure AD B2C. Политика считывает параметр и отправляет его значение в шаблон HTML5. 

### <a name="step-81-add-a-content-definition-parameter"></a>Шаг 8.1. Добавление параметра определения содержимого

Добавьте элемент `ContentDefinitionParameters` следующим образом:
1. Откройте файл политики *SignUpOrSignin* (например, *SignUpOrSignin.xml*).

2. Найдите узел `<DefaultUserJourney>`. 

3. В узел `<DefaultUserJourney>` добавьте следующий фрагмент кода XML:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>Шаг 8.2. Изменение кода так, чтобы принять параметр строки запроса и заменить фоновое изображение
Измените метод `unified` HomeController для приема параметра campaignId. Затем метод проверяет значение параметра и устанавливает соответствующее значение для переменной `ViewData["background"]`.

1. Откройте файл *Controllers\HomeController.cs*, а затем измените метод `unified`, добавив следующий фрагмент кода:

    ```csharp
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

2. Найдите элемент `<img>` с идентификатором `background_background_image` и замените значение `src` на `@ViewData["background"]`.

    ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>Шаг 8.3. Отправка изменений и публикация политики
1. Опубликуйте проект Visual Studio в службе приложений Azure.

2. Отправьте файл политики *SignUporSignIn.xml* в Azure AD B2C.

3. Откройте **B2C_1A_signup_signin**, отправленную вами пользовательскую политику проверяющей стороны, а затем выберите **Запустить сейчас**.  
    Вы увидите то же фоновое изображение, которое было показано ранее.

4. Скопируйте URL-адрес из адресной строки браузера.

5. Добавьте параметр строки запроса _campaignId_ в универсальный код ресурса (URI). Например, добавьте `&campaignId=hawaii`, как показано на следующем рисунке:

    ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Нажмите клавишу **ВВОД**, чтобы отобразить фон Гавайев.

    ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Измените значение на *Tokyo* и нажмите клавишу **ВВОД**.  
    В браузере отобразится фон Токио.

    ![Изменение фона страницы](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>Шаг 9. Изменение остальной части пути взаимодействия пользователя
Если выбрать ссылку **Зарегистрироваться сейчас** на странице входа, в браузере отобразится фоновое изображение по умолчанию, а не определенное изображение. Это связано с тем, что вы изменили только страницу регистрации или входа в систему. Чтобы изменить остальную часть определений содержимого с самостоятельным утверждением, сделайте следующее:
1. Вернитесь к шагу 2 и выполните следующие действия.

    a. Скачайте файл *selfasserted*.

    Б. Скопируйте содержимое файла.

    c. Создайте представление *selfasserted*.

    d. Добавьте *selfasserted* в контроллер **Home**.

2. Вернитесь к шагу 4 и выполните следующие действия. 

    a. В политике расширения найдите узел `<ContentDefinition>`, который содержит `Id="api.selfasserted"`, `Id="api.localaccountsignup"` и `Id="api.localaccountpasswordreset"`.

    Б. Задайте для атрибута `LoadUri` универсальный код ресурса (URI) *selfasserted*.

3. Вернитесь к шагу 8.2 и измените код так, чтобы принимать параметры строки запроса, но на этот раз укажите функцию *selfasserted*. 

4. Отправьте политику *TrustFrameworkExtensions.xml* и немного подождите, чтобы удостовериться в отсутствии сбоя при проверке.

5. Выполните тест политики и выберите **Зарегистрироваться сейчас**, чтобы просмотреть результат.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Скачивание полного текста файлов политики и кода (необязательно)
* После того как вы ознакомитесь с [пошаговым руководством по началу работы с пользовательскими политиками](active-directory-b2c-get-started-custom.md), рекомендуем создать свой сценарий, используя собственные файлы пользовательской политики. Для справки мы предоставили [образцы файлов политики](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Вы можете загрузить полный код из примера решения Visual Studio [отсюда](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




