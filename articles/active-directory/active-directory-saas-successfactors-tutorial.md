---
title: Учебник. Интеграция Azure Active Directory с SuccessFactors | Microsoft Docs
description: Узнайте, как использовать SuccessFactors с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и многого другого.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2016
ms.author: jeedes

---
# Руководство. Интеграция Azure Active Directory с SuccessFactors
Цель этого руководства — показать, как интегрировать приложение SuccessFactors с Azure Active Directory (Azure AD).

Интеграция SuccessFactors с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к приложению SuccessFactors.
* Вы можете включить автоматический вход пользователей в SuccessFactors (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Предварительные требования
Чтобы настроить интеграцию Azure AD с SuccessFactors, вам потребуется:

* Действующая подписка на Azure
* клиент в SuccessFactors.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SuccessFactors из коллекции.
2. Настройка и проверка единого входа в Azure AD

## Добавление SuccessFactors из коллекции
Чтобы настроить интеграцию SuccessFactors с Azure AD, необходимо добавить SuccessFactors из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SuccessFactors из коллекции, выполните следующие действия.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Настройка единого входа][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Настройка единого входа][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
   ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.
   
   ![Настройка единого входа][4]
6. В **поле поиска** введите **SuccessFactors**.
   
   ![Настройка единого входа][5]
7. В области результатов выберите **SuccessFactors** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Настройка единого входа][6]

## Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в приложении SuccessFactors с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SuccessFactors соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SuccessFactors.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SuccessFactors.

Чтобы настроить и проверить единый вход Azure AD в SuccessFactors, вам потребуется выполнить действия, описанные в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SuccessFactors](#creating-a-successfactors-test-user)** требуется для создания пользователя Britta Simon в SuccessFactors, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении SuccessFactors.

**Чтобы настроить единый вход Azure AD в SuccessFactors, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **SuccessFactors** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа][7]
2. На странице **Как пользователи должны входить в SuccessFactors?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа][8]
3. На странице **Настройка URL-адреса приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
   ![Настройка единого входа][9]
   
   а. В текстовое поле **URL-адрес для входа** введите URL-адрес в любом из следующих форматов:
   
   |  |
   | --- |
   | `https://<company name>.successfactors.com/<company name>` |
   | .`https://<company name>.sapsf.com/<company name>` |
   | .`https://<company name>.successfactors.eu/<company name>` |
   | .`https://<company name>.sapsf.eu` |
   
   b. В текстовое поле **URL-адрес ответа** введите URL-адрес в любом из следующих форматов:
   
   |  |
   | --- |
   | .`https://<company name>.successfactors.com/<company name>` |
   | .`https://<company name>.sapsf.com/<company name>` |
   | .`https://<company name>.successfactors.eu/<company name>` |
   | .`https://<company name>.sapsf.eu` |
   | .`https://<company name>.sapsf.eu/<company name>` |
   
   c. Нажмите кнопку **Далее**.

    > [AZURE.TIP] Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения на фактические URL-адреса для входа и ответа. Чтобы узнать эти адреса, обратитесь в [службу поддержки SuccessFactors](https://www.successfactors.com/en_us/support.html).

1. На странице **Настройка единого входа в SuccessFactors** нажмите кнопку **Загрузить сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа][10]
2. В другом окне веб-браузера войдите на **портал администрирования SuccessFactors** с правами администратора.
3. Откройте раздел **Application Security** (Безопасность приложения) и найдите в нем **функцию единого входа**.
4. Введите любое значение в поле **Reset Token** (Сброс маркера) и нажмите кнопку **Save Token** (Сохранить маркер), чтобы включить единый вход SAML.
   
    ![Настройка единого входа на стороне приложения][11]

    > [AZURE.NOTE] Это значение используется только как признак включения. Если здесь сохранено любое значение, единый вход SAML включен. Если сохранено пустое значение, единый вход SAML отключен.

1. Перейдите на экран, представленный на снимке ниже, и выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения][12]
   
    а. Установите переключатель **SAML v2 SSO** (Единый вход SAML версии 2).
   
    b. Задайте имя утверждающей стороны SAML (например, "Издатель SAML + название компании").
   
    c. В текстовое поле **SAML Issuer** (Издатель SAML) вставьте **URL-адреса издателя** из мастера настройки приложения в Azure AD.
   
    г) Выберите значение **Response(Customer Generated/IdP/AP)** (Ответ (создается клиентом/IdP/AP)) для параметра **Require Mandatory Signature** (Требуется обязательная подпись).
   
    д. Выберите значение **Enabled** (Включено) для параметра **Enable SAML Flag** (Включить флаг SAML).
   
    Е. Выберите значение **No** (Нет) для параметра **Login Request Signature (SF Generated/SP/RP)** (Подпись запроса на вход (создается SF/SP/RP)).
   
    g. Выберите значение **Browser/Post Profile** (Браузер/пост-профилирование) для параметра **SAML Profile** (Профиль SAML).
   
    h. Выберите значение **No** (Нет) для параметра **Enforce Certificate Valid Period** (Применять период действия сертификата).
   
    i. Скопируйте содержимое скачанного файла сертификата и вставьте его в текстовое поле **SAML Verifying Certificate** (Сертификат для проверки SAML).

    > [AZURE.NOTE] Содержимое сертификата должно включать открывающий и закрывающий теги сертификата.

1. Перейдите в раздел SAML V2 и выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения][13]
   
    а. Выберите значение **Yes** (Да) для параметра **Support SP-initiated Global Logout** (Поддержка глобального выхода, инициированного SP).
   
    b. В текстовое поле **Global Logout Service URL (LogoutRequest destination)** (URL-адрес службы глобального выхода) вставьте значение **URL-адреса удаленного выхода** из мастера настройки приложения в Azure AD.
   
    c. Выберите значение **No** (Нет) для параметра **Require sp must encrypt all NameID element** (Требуется шифрование всех элементов NameID на стороне SP).
   
    г) Выберите значение **unspecified** (Не определено) для параметра **NameID Format** (Формат идентификатора имени).
   
    д. Выберите значение **Yes** (Да) для параметра **Enable sp initiated login (AuthnRequest)** (Включить вход по требованию SP).
   
    Е. В текстовое поле **Send request as Company-Wide issuer** (Отправлять запрос как издатель в пределах компании) вставьте **URL-адрес удаленного входа** из мастера настройки приложения в Azure AD.
2. Чтобы в именах для входа не учитывался регистр, выполните следующие действия.
   
   а) Найдите в нижней части экран раздел **Company Settings** (Параметры компании).
   
   б) Установите флажок **Enable Non-Case-Sensitive Username** (Имя пользователя без учета регистра).
   
   в) Щелкните **Save** (Сохранить).
   
   ![Настройка единого входа][29]

    > [AZURE.NOTE] При попытке включить этот параметр система проверит, не возникнут ли повторяющиеся имена для входа SAML. Предположим, что у клиента есть пользователи с именами User1 и user1. Если отменить учет регистра, эти имена будут считаться одинаковыми. В таком случае система выдаст сообщение об ошибке и параметр не будет включен. Клиенту потребуется изменить одно из таких имен пользователя, чтобы они имели различное написание.

1. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Приложения][14]
2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
   ![Приложения][15]

### Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][16]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD][17]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD][18]
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD][19]
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD][20]
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовое поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD][21]
   
   а. В текстовом поле **Имя** введите **Britta**.
   
   b. В текстовое поле **Фамилия** введите **Simon**.
   
   c. В текстовое поле **Отображаемое имя** введите **Britta Simon**.
   
   г) В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD][22]
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD][23]
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.

### Создание тестового пользователя SuccessFactors
Чтобы пользователи Azure AD могли выполнять вход в SuccessFactors, они должны быть подготовлены для работы с SuccessFactors. В случае SuccessFactors подготовка пользователей осуществляется вручную.

Чтобы создать пользователей в SuccessFactors, необходимо обратиться в [службу поддержки SuccessFactors](https://www.successfactors.com/en_us/support.html).

### Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SuccessFactors.

![Назначение пользователя][24]

**Чтобы назначить пользователя Britta Simon в SuccessFactors, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][25]
2. Из списка приложений выберите **SuccessFactors **.
   
    ![Настройка единого входа][26]
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][27]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][28]

### Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SuccessFactors на панели доступа, вы автоматически войдете в приложение SuccessFactors.

## Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

<!---HONumber=AcomDC_0817_2016-->
