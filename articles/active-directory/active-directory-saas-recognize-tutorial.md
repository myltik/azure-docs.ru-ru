---
title: "Руководство по интеграции Azure Active Directory с Recognize | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Recognize."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a2ce8c3a6aad76ca491ca17beab8555ad77a1142
ms.openlocfilehash: 3bfb340639674406a0a983106b56ca4a993aaf14
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Руководство по интеграции Azure Active Directory с Recognize
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Recognize.

Интеграция Recognize с Azure AD обеспечивает следующие преимущества:

* с помощью Azure AD вы можете контролировать доступ к Recognize;
* вы можете включить автоматический вход пользователей в Recognize (единый вход) под учетной записью Azure AD;
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Recognize, вам потребуется:

* подписка Azure AD;
* подписка на Recognize с поддержкой единого входа.

 >[!NOTE]
 >Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
 > 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Recognize из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-recognize-from-the-gallery"></a>Добавление Recognize из коллекции.
Чтобы настроить интеграцию Recognize с Azure AD, необходимо добавить Recognize из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Recognize из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Recognize **.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. На панели результатов выберите **Recognize ** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Recognize с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Recognize соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Recognize.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Recognize.

Чтобы настроить и проверить единый вход Azure AD в Recognize, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Recognize](#creating-a-recognize-test-user)** требуется для создания пользователя Britta Simon в Recognize, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Recognize.

**Чтобы настроить единый вход Azure AD в Recognize, сделайте следующее:**

1. На классическом портале на странице интеграции с приложением **Recognize** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Recognize?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. На странице диалогового окна **Настроить параметры приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://recognizeapp.com/<your-domain>/saml/sso`. 
  2. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://recognizeapp.com/<your-domain>/saml/metadata`.
  3. Нажмите кнопку **Далее**.
  
    >[!NOTE]
    >Если вы не знаете эти URL-адреса, введите примеры URL-адресов из шаблона. Чтобы узнать эти значения, перейдите к шагу 9 или свяжитесь с разработчиком Recognize по адресу <mailto:support@recognizeapp.com>.
    >

4. На странице **Настройка единого входа в Recognize** щелкните **Скачать сертификат** и сохраните полученный файл на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. В другом окне веб-браузера войдите на корпоративный сайт Recognize с правами администратора.
6. В правом верхнем углу щелкните **Menu** (Меню). Перейдите к пункту **Company Admin** (Администратор компании).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. В левой панели навигации щелкните **Settings**(Параметры).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. В разделе **SSO Settings** (Параметры единого входа) выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
 1. Для параметра **Enable SSO** (Включить единый вход) установите значение **ON** (Включено).
 2. В текстовое поле **IDP Entity ID** (Идентификатор сущности поставщика удостоверений) введите **Issuer URL** (URL-адрес издателя) из мастера настройки приложения в Azure AD. 
 3. В текстовое поле **SSO target URL** (Целевой адрес единого входа) введите значение **URL-адреса службы единого входа** из мастера настройки приложения Azure AD. 
 4. В текстовое поле **SLO target URL** (Целевой адрес единого выхода) введите значение **URL-адреса службы единого выхода** из мастера настройки приложения Azure AD. 
 5. Откройте загруженный файл сертификата в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Certificate** (Сертификат).  
 6. Нажмите кнопку **Сохранить параметры** . 
9. Кроме сведений в разделе **SSO Settings** (Параметры единого входа), сохраните URL-адрес из раздела **Service Provider Metadata url** (URL-адрес метаданных поставщика службы).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. Откройте ссылку с **URL-адресом метаданных** в новом окне браузера, чтобы скачать документ метаданных. Используйте значение EntityDescriptor, предоставленное приложением Recognize для параметра **Identifier** (Идентификатор) в диалоговом окне **Configure App Settings** (Настройка параметров приложения).
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]
12. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png) 
 1. В текстовом поле **Имя** введите **Britta**.   
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**. 
 5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png) 
 1. Запишите значение поля **Новый пароль**. 
 2. Нажмите **Завершено**.   

### <a name="create-a-recognize-test-user"></a>Создание тестового пользователя Recognize
Чтобы пользователи Azure AD могли выполнять вход в Recognize, они должны быть подготовлены для Recognize. Для Recognize подготовка выполняется вручную.

Это приложение не поддерживает подготовку SCIM, но предусматривает альтернативный механизм синхронизации для подготовки пользователей. 

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на корпоративный сайт Recognize в качестве администратора.
2. В правом верхнем углу щелкните **Menu** (Меню). Перейдите к пункту **Company Admin** (Администратор компании).
3. В левой панели навигации щелкните **Settings**(Параметры).
4. В разделе **User Sync** (Синхронизация пользователей) выполните следующие действия.
   
   ![Новый пользователь](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "Новый пользователь")   
 1. В поле **Sync Enabled** (Синхронизация включена) выберите **ON** (Включено). 
 2. Для параметра **Choose sync provider** (Выбор поставщика синхронизации) выберите значение **Microsoft/Office 365**. 
 3. Щелкните **Run User Sync** (Запустить синхронизацию пользователей).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход в Azure, предоставив доступ к Recognize.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Recognize, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Recognize**.
   
    ![Настройка единого входа](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Recognize на панели доступа, вы автоматически войдете в приложение Recognize.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png

