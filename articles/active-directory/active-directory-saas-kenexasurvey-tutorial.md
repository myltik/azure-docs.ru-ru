---
title: "Учебник. Интеграция Azure Active Directory с IBM Kenexa Survey Enterprise | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в IBM Kenexa Survey Enterprise."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 02a3fd20c1b951ba175760b64d8f8bc89cd25eb3
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Руководство по интеграции Azure Active Directory с IBM Kenexa Survey Enterprise
В этом руководстве описано, как интегрировать IBM Kenexa Survey Enterprise с Azure Active Directory (Azure AD).

Интеграция IBM Kenexa Survey Enterprise с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD можно контролировать, кто будет иметь доступ к IBM Kenexa Survey Enterprise.
* Вы можете включить автоматический вход пользователей в IBM Kenexa Survey Enterprise (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с IBM Kenexa Survey Enterprise, вам потребуется следующее:

* подписка Azure AD;
* подписка на IBM Kenexa Survey Enterprise с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

1. Добавление IBM Kenexa Survey Enterprise из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Добавление IBM Kenexa Survey Enterprise из коллекции.
Чтобы настроить интеграцию IBM Kenexa Survey Enterprise в Azure AD, необходимо добавить IBM Kenexa Survey Enterprise из коллекции в список управляемых приложений SaaS.

**Чтобы добавить IBM Kenexa Survey Enterprise из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **IBM Kenexa Survey Enterprise**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_01.png)
7. В области результатов выберите **IBM Kenexa Survey Enterprise**, а затем нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в IBM Kenexa Survey Enterprise с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в IBM Kenexa Survey Enterprise соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в IBM Kenexa Survey Enterprise.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в IBM Kenexa Survey Enterprise.

Чтобы настроить и проверить единый вход Azure AD в IBM Kenexa Survey Enterprise, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя IBM Kenexa Survey Enterprise](#creating-an-kenexasurvey-test-user)** требуется для создания пользователя Britta Simon в IBM Kenexa Survey Enterprise, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении IBM Kenexa Survey Enterprise.

**Чтобы настроить единый вход Azure AD в IBM Kenexa Survey Enterprise, выполните следующие действия.**

1. На странице интеграции с приложением **IBM Kenexa Survey Enterprise** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в IBM Kenexa Survey Enterprise** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_03.png)
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_04.png)
  1. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://surveys.kenexa.com/<company code>` 
  2. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://surveys.kenexa.com/<company code>/tools/sso.asp`.
  3. Нажмите кнопку **Далее**.
   
   >[!NOTE]
   >Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо указать фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в службу поддержки IBM Kenexa Survey Enterprise. 
   > 
4. На странице **Настройка единого входа в IBM Kenexa Survey Enterprise** щелкните **Скачать сертификат** и сохраните полученный файл на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_05.png) 
5. Для настройки единого входа для своего приложения обратитесь к группе поддержки IBM Kenexa и предоставьте следующие сведения:
 * Скачанный файл сертификата
 * **URL-адрес издателя**  
 * **URL-адрес единого входа SAML**
 * **URL-адрес службы единого выхода**
   
  >[!NOTE]
  >Обратите внимание, что значение утверждения NameID в ответе должно соответствовать идентификатору единого входа, настроенному в системе Kenexa. Поэтому поработайте с группой поддержки Kenexa, чтобы сопоставить соответствующий идентификатор пользователя в вашей организации как идентификатор единого входа. По умолчанию Azure AD задаст NameIdentifier в качестве значения UPN. Это можно изменить на вкладке "Атрибут", как показано на следующем снимке экрана. Интеграция будет работать только после завершения правильного сопоставления. 
  > 
   
    ![Настройка единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_51.png)
6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]
8. На странице интеграции с приложением **IBM Kenexa Survey Enterprise** классического портала Azure в меню вверху щелкните **Атрибуты**.
   
    ![Настройка единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_06.png)
9. В диалоговом окне **Атрибуты токена SAML** сделайте следующее.
 1. Выберите атрибут **NameIdentifier** и щелкните значок **Изменить**.
 
    ![Настройка единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_07.png)  
 2. В списке **Значение атрибута** введите идентификатор единого входа, настроенный в системе Kenexa.  
 3. Нажмите **Завершено**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_05.png) 
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации». 
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_06.png)  
 1. В текстовом поле **Имя** введите **Britta**.    
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**. 
 5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_08.png)  
 1. Запишите значение поля **Новый пароль**.  
 2. Нажмите **Завершено**.   

### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Создание тестового пользователя IBM Kenexa Survey Enterprise
В этом разделе описано, как создать пользователя Britta Simon в приложении IBM Kenexa Survey Enterprise. 

Поработайте с группой поддержки IBM Kenexa, чтобы сопоставить идентификатор единого входа для всех пользователей. Также это значение идентификатора единого входа необходимо сопоставить со значением NameIdentifier из Azure AD. Эти заданные по умолчанию параметры можно изменить на вкладке "Атрибут".

>[!NOTE]
>Если требуется создать пользователя вручную, необходимо обратиться к группе поддержки IBM Kenexa Survey Enterprise. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к IBM Kenexa Survey Enterprise.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в IBM Kenexa Survey Enterprise, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **IBM Kenexa Survey Enterprise**.
   
    ![Настройка единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку IBM Kenexa Survey Enterprise на панели доступа, вы автоматически войдете в приложение IBM Kenexa Survey Enterprise.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_205.png

