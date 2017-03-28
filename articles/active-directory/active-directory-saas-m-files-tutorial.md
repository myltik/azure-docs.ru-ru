---
title: "Учебник. Интеграция Azure Active Directory с M-Files | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в M-Files."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 78f0a065b675326a9a507e0cf480bc779fd458a9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Учебник. Интеграция Azure Active Directory с M-Files
В этом учебнике описано, как интегрировать приложение M-Files с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением M-Files обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к M-Files.
* Вы можете включить автоматический вход пользователей в M-Files (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с M-Files, вам потребуется:

* подписка Azure AD;
* подписка на **M-Files** с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление M-Files из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-m-files-from-the-gallery"></a>Добавление M-Files из коллекции
Чтобы настроить интеграцию M-Files с Azure AD, необходимо добавить M-Files из коллекции в список управляемых приложений SaaS.

**Чтобы добавить M-Files из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **M-Files**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_01.png)
7. В области результатов выберите **M-Files** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе можно настроить и проверить единый вход Azure AD в M-Files с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в M-Files соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в M-Files.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в M-Files. Чтобы настроить и проверить единый вход Azure AD в M-Files, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя M-Files](#creating-a-m-file-test-user)** требуется для создания в M-Files пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении M-Files.

**Чтобы настроить единый вход Azure AD в M-Files, выполните следующие действия:**

1. На странице интеграции с приложением **M-Files** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7] 
2. На странице **Как пользователи должны входить в M-Files?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_06.png)
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_07.png)
  1. В текстовом поле "URL-адрес для входа" введите URL-адрес в следующем формате: `https://<tenant-name>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в M-Files** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_09.png)
5. Для настройки единого входа в вашем приложении обратитесь в службу поддержки M-Files по адресу <mailto:support@m-files.com> и предоставьте скачанный файл метаданных.
   
   >[!NOTE]
   >Если вы хотите настроить единый вход для классического приложения M-Files, то выполните описанные ниже действия. Если единый вход настраивается только для веб-версии M-Files, то никакие дополнительные действия не требуются.  
   > 
6. Выполните описанные ниже действия, чтобы настроить в классическом приложении M-Files единый вход с помощью Azure AD. Для скачивания M-Files перейдите на страницу [M-Files download](https://www.m-files.com/en/download-latest-version) (Скачивание M-Files).
7. Откройте окно **M-Files Desktop Settings** (Параметры классического приложения M-Files). Нажмите кнопку **Add** (Добавить).
   
    ![Настройка единого входа](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)
8. В окне **Document Vault Connection Properties** (Свойства подключения хранилища документов) выполните указанные ниже действия.
   
  ![Настройка единого входа](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  
    В разделе **Server** (Сервер) введите следующие значения:  
 1. В поле **Name** (Имя) введите `<tenant-name>.cloudvault.m-files.com`.  
 2. В поле **Port Number** (Номер порта) введите **4466**. 
 3. Для параметра **Protocol** (Протокол) выберите **HTTPS**. 
 4. В поле **Authentication** (Аутентификация) выберите **Specific Windows user** (Определенный пользователь Windows). Затем отобразится страница подписи. Введите свои учетные данные Azure AD. 
 5. Для параметра **Vault on Server** (Хранилище на сервере) выберите соответствующее хранилище на сервере. 
 6. Нажмите кнопку **ОК**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_05.png) 
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_06.png)  
 1. В текстовом поле **Имя** введите **Britta**.   
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**. 
 5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_08.png)  
 1. Запишите значение поля **Новый пароль**. 
 2. Нажмите **Завершено**.   

### <a name="create-a-m-files-test-user"></a>Создание тестового пользователя M-Files
В этом разделе описано, как создать пользователя Britta Simon в приложении M-Files. Если вы не знаете, как создать пользователя в M-Files, обратитесь в службу поддержки M-Files по адресу <mailto:support@m-files.com>.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к M-Files, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в M-Files, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **M-Files**.
   
    ![Настройка единого входа](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_12.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке "Все пользователи" выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент M-Files на панели доступа, вы автоматически войдете в приложение M-Files.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-m-files-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_205.png

