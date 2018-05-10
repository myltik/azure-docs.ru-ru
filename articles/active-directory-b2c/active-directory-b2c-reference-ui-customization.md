---
title: 'Azure AD B2C: настройка пользовательского интерфейса | Документация Майкрософт'
description: В этом разделе описаны возможности настройки пользовательского интерфейса в Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: b202f30e5fb47bcd16f25c5961f8345dd0324139
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C: настройка пользовательского интерфейса Azure AD B2C

Взаимодействие с пользователем имеет первостепенную важность в клиентском приложении.  Создавайте решения с фирменной символикой и расширяйте клиентскую базу. Azure Active Directory B2C (Azure AD B2C) позволяет настраивать страницы регистрации, входа, изменения профиля и сброса пароля с идеальной точностью.

> [!NOTE]
> Возможность настройки пользовательского интерфейса страницы, описанная в этой статье, не применяется к политике только для входа, связанной странице сброса пароля и проверочным сообщениям электронной почты.  Вместо этого в ней используется [функция настройки фирменной символики компании](../active-directory/customize-branding.md).
>
> Как и при инициировании политики изменения профиля *перед* входом, пользователь будет перенаправлен на страницу, которую можно настраивать с помощью [функции настройки фирменной символики компании](../active-directory/customize-branding.md).

В этой статье рассматриваются следующие темы:

* Возможность настройки пользовательского интерфейса.
* Инструмент для передачи HTML-содержимого в хранилище BLOB-объектов Azure, использующийся вместе с возможностью настройки страницы пользовательского интерфейса.
* Элементы пользовательского интерфейса, используемые Azure AD B2C, которые можно настроить с помощью каскадных таблиц стилей (CSS).
* рекомендации по использованию этих возможностей.

## <a name="the-page-ui-customization-feature"></a>Возможность настройки пользовательского интерфейса

Вы можете изменять внешний вид страниц регистрации, входа в систему (см. примечание выше для исключений, связанных с брендингом), сброса пароля и изменения профиля клиента с помощью настройки [политик](active-directory-b2c-reference-policies.md). При переходе между приложением и страницами, которые обслуживает Azure AD B2C, ваши клиенты не заметят никакой разницы в работе.

В отличие от других служб с параметрами пользовательского интерфейса в Azure AD B2C предусмотрен простой и современный подход к настройке пользовательского интерфейса.

Вот как это работает. Служба Azure AD B2C выполняет код в браузере пользователя и использует современный метод — [общий доступ к ресурсам независимо от источника (CORS)](http://www.w3.org/TR/cors/).  Во время выполнения содержимое загружается с URL-адреса, указанного в политике. Для разных страниц можно указать разные URL-адреса. После объединения содержимого, загруженного с URL-адреса, с HTML-фрагментом, вставленным из Azure AD B2C, страница отображается клиенту. Что следует сделать.

1. Создайте корректное содержимое HTML5 и добавьте в раздел `<body>` пустой элемент `<div id="api"></div>`. Этот элемент обозначает место, в которое будет добавляться содержимое Azure AD B2C.
1. Затем разместите это содержимое в конечной точке HTTPS (CORS-доступ должен быть включен). Обратите внимание, что при настройке CORS необходимо включить методы запроса GET и OPTIONS.
1. Измените стиль элементов пользовательского интерфейса, которые вставляет Azure AD B2C, с помощью каскадных таблиц стилей.

### <a name="a-basic-example-of-customized-html"></a>Простой пример настраиваемого содержимого HTML

В следующем примере показано самое простое содержимое HTML, которое можно использовать для тестирования этой возможности. Используйте [вспомогательный инструмент](active-directory-b2c-reference-ui-customization-helper-tool.md) для передачи и настройки содержимого в хранилище BLOB-объектов Azure. Вы можете убедиться, что базовые нестилизованные кнопки и поля форм на каждой странице отображаются и работают.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>Тестирование возможности настройки пользовательского интерфейса

Хотите протестировать функцию настройки пользовательского интерфейса с помощью примера содержимого HTML и CSS?  Используйте [вспомогательный инструмент](active-directory-b2c-reference-ui-customization-helper-tool.md) для отправки и настройки примера содержимого в хранилище BLOB-объектов Azure.

> [!NOTE]
> Вы можете разместить содержимое пользовательского интерфейса в любом расположении: на веб-серверах, CDN, AWS S3, системах обмена файлами и т. д. После того как вы разместите содержимое на общедоступной конечной точке HTTPS (CORS-доступ должен быть включен), можете приступать к тестированию. Хранилище BLOB-объектов Azure используется исключительно в качестве примера.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>Фрагменты пользовательского интерфейса, внедренные с помощью Azure AD B2C

В разделах ниже приведены фрагменты кода HTML5, которые служба Azure AD B2C добавляет в элемент `<div id="api"></div>` в вашем содержимом. **Не вставляйте эти фрагменты в содержимое HTML5.** Служба Azure AD B2C вставляет их в во время выполнения. Используйте их для справки при разработке собственных каскадных таблиц стилей (CSS).

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>Фрагмент, вставленный на страницу выбора поставщика удостоверений

На этой странице содержится список поставщиков удостоверений, которых можно выбирать во время регистрации или входа пользователя. На соответствующих кнопках отмечены поставщики удостоверений социальных сетей, например Facebook и Google+, или локальных учетных записей (адрес электронной почты или имя пользователя).

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>Фрагмент, вставленный на страницу регистрации локальной учетной записи

Эта страница содержит форму для регистрации локальной учетной записи по адресу электронной почты или имени пользователя. Форма может содержать различные элементы управления для ввода текста, например: текстовое поле, поле ввода пароля, переключатель, раскрывающийся список с единственным выбором или флажки множественного выбора.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>Фрагмент, вставленный на страницу регистрации учетной записи социальных сетей

Эта страница появляется при регистрации с помощью имеющейся учетной записи от поставщика удостоверений в социальных сетях, например Facebook или Google+.  Она используется, если нужно собрать дополнительные сведения о пользователе с использованием формы регистрации. Эта страница аналогична странице регистрации локальных учетных записей (см. предыдущий раздел), за исключением полей для ввода пароля.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Фрагмент, вставленный на единую страницу регистрации и входа

На этой странице обрабатывается регистрация и вход клиентов, которые могут использовать поставщики удостоверений, такие как Facebook или Google+, или локальные учетные записи.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>Фрагмент, вставленный на страницу Многофакторной идентификации

Эта страница позволяет пользователю подтвердить свой номер телефона (с помощью SMS-сообщения или голосового звонка) во время регистрации или входа.

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>Фрагмент, вставленный на страницу ошибки

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>Локализация содержимого HTML

Локализация HTML-содержимого выполняется двумя способами. Одним из способов является включение [настройка языка](active-directory-b2c-reference-language-customization.md). Таким образом, Azure AD B2C сможет переадресовать параметр подключения Open ID Connect, `ui-locales` на конечную точку.  Сервер содержимого может использовать этот параметр для предоставления настроенных страниц HTML на конкретном языке.

Кроме того, можно извлечь содержимое из разных расположений, в зависимости от используемого языкового стандарта. В конечной точке с поддержкой CORS можно настроить структуру папок для размещения содержимого для определенных языков. При использовании шаблона подстановки `{Culture:RFC5646}` запрос будет правильным.  Например, предположим, что это URI вашей настраиваемой страницы.

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Страницу можно загрузить на `fr`. Когда страница подгружает содержимое HTML и CSS, она запрашивает его из:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="things-to-remember-when-building-your-own-content"></a>Рекомендации по созданию собственного содержимого

Если вы планируете использовать возможности настройки пользовательского интерфейса страницы, изучите следующие рекомендации.

* Не копируйте стандартное содержимое Azure AD B2C и не пытайтесь изменить его. Лучше создать содержимое HTML5 с нуля, используя стандартное содержимое в качестве образца.
* В целях безопасности мы запрещаем добавлять в содержимое какие-либо сценарии JavaScript. Большинство необходимых вам возможностей уже доступны. Если вам не хватает функций, сообщите нам, используя сайт [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) .
* Поддерживаемые версии браузеров:
  * Internet Explorer 11, 10, Edge
  * Ограниченная поддержка Internet Explorer 9, 8
  * Google Chrome 42.0 и выше
  * Mozilla Firefox 38.0 и выше
* Убедитесь, что в HTML не включены теги `<form>`. Они будут мешать операциям POST, созданным на основе внедренного кода HTML из Azure AD B2C.
