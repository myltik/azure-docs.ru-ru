---
title: "Руководство по интеграции Azure Active Directory с Questetra BPM Suite | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Questetra BPM Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 7ae75446c9d19ce15a82caa9604658a528ab9941
ms.contentlocale: ru-ru
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Учебник. Интеграция Azure Active Directory с Questetra BPM Suite
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Questetra BPM Suite.  
Интеграция Questetra BPM Suite с Azure AD обеспечивает следующие преимущества: 

* С помощью Azure AD вы можете контролировать доступ к Questetra BPM Suite. 
* Вы можете включить автоматический вход пользователей в Questetra BPM Suite (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Questetra BPM Suite, вам потребуется следующее:

* подписка Azure AD;
* Подписка [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) с включенным единым входом.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление Questetra BPM Suite из коллекции 
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Добавление Questetra BPM Suite из коллекции
Чтобы настроить интеграцию Questetra BPM Suite в Azure AD, необходимо добавить Questetra BPM Suite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Questetra BPM Suite из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **Questetra BPM Suite**.
   
    ![Приложения][5]

7. В области результатов выберите **Questetra BPM Suite** и щелкните **Завершить**, чтобы добавить приложение.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD в Questetra BPM Suite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Questetra BPM Suite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Questetra BPM Suite.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Questetra BPM Suite.

Чтобы настроить и проверить единый вход Azure AD в Questetra BPM Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы позволить пользователям использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Questetra BPM Suite](#creating-a-questetra-bpm-suite-test-user)** требуется для создания соответствующего пользователя Britta Simon в Questetra BPM Suite, связанного с ее представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Questetra BPM Suite.

**Чтобы настроить единый вход Azure AD в Questetra BPM Suite, выполните следующие действия.**

1. На странице интеграции с приложением **Questetra BPM Suite** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][8]

2. На странице **Как пользователи должны входить в Questetra BPM Suite?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][9]

3. В другом окне веб-браузера войдите в свой корпоративный веб-сайт **Questetra BPM Suite** в качестве администратора.

4. В верхнем меню щелкните пункт **Системные параметры**. 
   
    ![единого входа Azure AD][10]

5. Чтобы открыть страницу **SingleSignOnSAML**, щелкните **SSO (SAML)** (Единый вход (SAML)). 
   
    ![единого входа Azure AD][11]

6. На диалоговой странице **Настройка параметров приложения** классического портала Azure выполните следующие действия: 
   
    ![Настройка параметров приложения][13]
   
    А. На веб-сайте компании **Questetra BPM Suite** в разделе SP Information (Сведения о SP) скопируйте значение поля **ACS URL** (URL-адрес ACS) и вставьте его в текстовом поле **URL-адрес входа**.
   
    b. На веб-сайте компании **Questetra BPM Suite** в разделе SP Information (Сведения о SP) скопируйте значение поля **Entity ID** (Идентификатор сущности) и вставьте его в текстовом поле **Issuer URL** (URL-адрес издателя).
   
    c. На веб-сайте компании **Questetra BPM Suite** в разделе SP Information (Сведения о SP) скопируйте значение поля **ACS URL** (URL-адрес ACS) и вставьте его в текстовом поле **Reply URL** (URL-адрес ответа).
   
    d. Нажмите кнопку **Далее**.

7. На странице **Настройка единого входа в Questetra BPM Suite** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата локально на компьютере.
   
    ![Настройка единого входа][14]

8. На веб-сайте компании **Questetra BPM Suite** выполните следующие действия: 
   
    ![Настройка единого входа][15]
   
    а. Выберите пункт **Включить единый вход**.
   
    b. На классическом портале Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовом поле **Идентификатор сущности**.
   
    c. На классическом портале Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовом поле **URL-адрес страницы входа**.
   
    d. На классическом портале Azure скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовом поле **URL-адрес страницы выхода**.
   
    д. В текстовом поле **NameID format** (Формат идентификатора имени) введите **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    Е. Создайте файл в кодировке Base-64 из загруженного сертификата. 

    >[!TIP] 
    >Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    g. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат проверки** . 

    h. Щелкните **Сохранить**.

1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
   
    ![Что такое Azure AD Connect?][17]

2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Что такое Azure AD Connect?][18]


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD][100] 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD][101] 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD][102] 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD][103]
   
    а. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку Далее.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD][104] 
   
    а. В текстовом поле **Имя** введите **Britta**. 
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD][105]  

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD][106]   
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-questetra-bpm-suite-test-user"></a>Создание тестового пользователя Questetra BPM Suite
Цель этого раздела — создать пользователя с именем Britta Simon в Questetra BPM Suite.

**Чтобы создать пользователя с именем Britta Simon в Questetra BPM Suite, выполните следующие действия.**

1. Выполните входа на сайт компании Questetra BPM Suite в качестве администратора.
2. Выберите **System Settings > User List > New User** (Системные параметры > Список пользователей > Новый пользователь). 
3. В диалоговом окне создания нового пользователя выполните следующие действия: 
   
    ![Создание тестового пользователя][300] 
   
    а. В текстовом поле **Имя** в Azure AD введите имя пользователя Britta.
   
    b. В текстовом поле **Электронная почта** в Azure AD введите адрес электронной почты пользователя Britta.
   
    c. В текстовом поле **Пароль** введите пароль.

4. Нажмите кнопку **Добавить нового пользователя**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход в Azure, предоставив ей доступ к Questetra BPM Suite.

![Что такое Azure AD Connect?][200]

**Чтобы назначить Britta Simon в Questetra BPM Suite, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в верхнем меню.
   
    ![Что такое Azure AD Connect?][201]
2. В списке приложений выберите **Questetra BPM Suite**.
   
    ![Что такое Azure AD Connect?][205]
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Что такое Azure AD Connect?][202]
4. В списке пользователей выберите **Britta Simon**.
   
    ![Что такое Azure AD Connect?][203]
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Что такое Azure AD Connect?][204]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув элемент Questetra BPM Suite на панели доступа, вы автоматически войдете в приложение Questetra BPM Suite.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

