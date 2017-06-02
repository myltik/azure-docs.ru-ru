---
title: "Учебник. Интеграция Azure Active Directory с Litmos | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Litmos."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2c967d759806b47862179ecf9e9a89801134192a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Руководство по интеграции Azure Active Directory с Litmos
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Litmos.  

Интеграция Azure AD с приложением Litmos обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к Litmos. 
* Вы можете включить автоматический вход пользователей в Litmos (единый вход) с их учетными записями Azure AD.
* Вы можете управлять учетными записями централизованно — через Azure Active Directory. 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Litmos, вам потребуется:

* подписка Azure AD;
* подписка Litmos с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  

Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

1. Добавление Litmos из коллекции 
2. Настройка и проверка единого входа в Azure AD

## <a name="add-litmos-from-the-gallery"></a>Добавление Litmos из коллекции
Чтобы настроить интеграцию Litmos с Azure AD, необходимо добавить Litmos из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Litmos из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Litmos**.
   
    ![Приложения][5]
7. В области результатов выберите **Litmos** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в Litmos с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Litmos соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Litmos.  

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Litmos.

Чтобы настроить и проверить единый вход Azure AD в Litmos, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Litmos](#creating-a-halogen-software-test-user)** требуется для создания в Litmos пользователя Britta Simon, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Litmos.  

В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  

Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

В рамках конфигурации необходимо настроить **атрибуты токена SAML** для приложения Litmos.  

![Единый вход в Azure AD][17] 

**Чтобы настроить единый вход Azure AD в Litmos, выполните следующие действия.**

1. На странице интеграции с приложением **Litmos** классического портала Azure AD щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Litmos?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7] 
3. Войдите на свой корпоративный сайт Litmos (например, *https://azureapptest.litmos.com/account/Login*) с правами администратора.
   
    ![единого входа Azure AD][21] 
4. В области навигации слева щелкните **Accounts**(Учетные записи).
   
    ![Единый вход в Azure AD][22] 
5. Щелкните вкладку **Integrations** (Интеграции).
   
    ![единого входа Azure AD][23] 
6. На вкладке **Integrations** (Интеграции) прокрутите страницу вниз до раздела **3rd Party Integrations** (Интеграции сторонних поставщиков), а затем откройте вкладку **SAML 2.0**.
   
    ![единого входа Azure AD][24] 
7. Скопируйте значение в разделе **The SAML endpoint for litmos is**(Конечная точка SAML для Litmos).
   
    ![Единый вход в Azure AD][26] 
8. На диалоговой странице **Настройка параметров приложения** классического портала Azure выполните следующие действия.
   
    ![единого входа Azure AD][8] 
   
    1. В текстовом поле **Идентификатор** введите URL-адрес, используемый для входа в приложение Litmos (например, *https://azureapptest.litmos.com/account/Login*).
   
    2. В текстовое поле **URL-адрес ответа** вставьте значение, скопированное из приложения Litmos на предыдущем шаге.
   
    3. Щелкните **Далее**.
9. На странице **Настройка единого входа в Litmos** выполните следующие действия.
   
    ![единого входа Azure AD][2] 
   
    * Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.
10. В приложении **Litmos** выполните следующие действия.
    
     ![единого входа Azure AD][25] 
    
     1. Выберите команду **Enable SAML**(Включить SAML).
    
     2. Создайте файл в кодировке **Base-64** из загруженного сертификата.  
    >[!TIP]
    >Дополнительные сведения вы можете узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
     >

     3. Откройте сертификат в кодировке Base 64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **SAML X.509 Certificate** (Сертификат SAML X.509).
    
     4. Нажмите кнопку **Сохранить изменения**.
11. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
    
     ![единого входа Azure AD][10]
12. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
     ![единого входа Azure AD][11]
13. В меню в верхней части экрана выберите пункт **Атрибуты** to open the **SAML Token Атрибуты** . 
    
    ![Настройка единого входа][12]
14. В диалоговом окне **Добавить атрибут пользователя** выполните следующие действия. 
    
    ![Настройка единого входа][14]
    
    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | Email |user.mail |
    | FirstName |user.givenname |
    | LastName |user.surname |
    
    Для каждой строки данных в таблице выше выполните следующие действия.
    
 1. Щелкните **добавить атрибут пользователя**.    
   ![Настройка единого входа][15]
 2. В текстовом поле **Имя атрибута** введите **имя атрибута**, отображаемое для этой строки.
 3. Выберите **значение атрибута** , отображаемое для этой строки.
 4. Щелкните **Завершить**, чтобы закрыть диалоговое окно **Добавить атрибут пользователя**.


1. Нажмите кнопку **Применить изменения**. 
   
   ![Настройка единого входа][16]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  
   
    1. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
   
    2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    3. Щелкните **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**.  
   
   2. В текстовом поле **Фамилия** введите **Simon**.
   
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
   4. В списке **Роль** выберите **Пользователь**.
   5. Щелкните **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
   
    1. Запишите значение поля **Новый пароль**.
   
    2. Нажмите **Завершено**.   

### <a name="create-a-litmos-test-user"></a>Создание тестового пользователя Litmos
Цель этого раздела — создать пользователя с именем Britta Simon в Litmos.  
Приложение Litmos поддерживает JIT-подготовку. Это значит, что учетная запись пользователя при необходимости создается автоматически во время попытки доступа к приложению с помощью панели доступа.

**Чтобы создать пользователя с именем Britta Simon в Litmos, выполните следующие действия.**

1. Войдите на свой корпоративный сайт Litmos (например, *https://azureapptest.litmos.com/account/Login*) с правами администратора.
   
    ![единого входа Azure AD][21] 
2. В области навигации слева щелкните **Accounts**(Учетные записи).
   
    ![Единый вход в Azure AD][22] 
3. Щелкните вкладку **Integrations** (Интеграции).
   
    ![единого входа Azure AD][23] 
4. На вкладке **Integrations** (Интеграции) прокрутите страницу вниз до раздела **3rd Party Integrations** (Интеграции сторонних поставщиков), а затем откройте вкладку **SAML 2.0**.
   
    ![единого входа Azure AD][24] 
5. Установите флажок **Autogenerate Users**(Автоматически создавать пользователей).
   
    ![единого входа Azure AD][27] 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к Litmos.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Litmos, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Litmos**.
   
    ![Назначение пользователя][202] 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент Litmos на панели доступа, вы автоматически войдете в приложение Litmos.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png






