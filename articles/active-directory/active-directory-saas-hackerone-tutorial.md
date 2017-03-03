---
title: "Учебник. Интеграция Azure Active Directory с HackerOne | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в HackerOne."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2fa331e94425673481689aefeb5393618f2471b9
ms.openlocfilehash: ced5f0fc2d9afa22f2d3ef3a81bb07482f69ec0e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Учебник. Интеграция Azure Active Directory с HackerOn
В этом учебнике описано, как интегрировать HackerOn с Azure Active Directory (Azure AD).

Интеграция HackerOn с Azure AD дает приведенные далее преимущества:

* С помощью Azure AD вы можете контролировать доступ к HackerOn.
* Вы можете включить автоматический вход пользователей в HackerOn (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с HackerOn, вам потребуется:

* Подписка Azure
* Подписка HackerOn с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится настройка и проверка единого входа Azure AD в тестовой среде.  

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

*  Добавление HackerOne из коллекции
*  Настройка и проверка единого входа Azure AD.

## <a name="add-hackerone-from-the-gallery"></a>Добавление HackerOne из коллекции
Чтобы настроить интеграцию HackerOn с Azure AD, необходимо добавить HackerOn из коллекции в список управляемых приложений SaaS.

**Чтобы добавить HackerOn из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

  ![Приложения][4]
6. В поле поиска введите **HackerOne**.

  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. В области результатов выберите **HackerOne** и нажмите кнопку **Завершить**, чтобы добавить приложение.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Далее выполняется настройка и проверка единого входа Azure AD в HackerOne с тестовым пользователем Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в HackerOn соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в HackerOne.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в HackerOne.

Чтобы настроить и проверить единый вход Azure AD в HackerOne, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя HackerOne](#creating-a-hackerone-test-user)** требуется для создания пользователя Britta Simon в HackerOne, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении HackerOne.

В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

**Чтобы настроить единый вход Azure AD в HackerOne, выполните следующие действия:**

1. На странице интеграции с приложением **HackerOne** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в HackerOne?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 
3. На странице диалогового окна **Настроить параметры приложения** выполните описанные ниже действия, после чего нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 
    1. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение HackerOne, в формате **https://hackerone.com/\<название_компании\>/authentication**. 
    2. Обратитесь в службу поддержки HackerOne по адресу [support@hackerone.com](mailto:support@hackerone.com) , чтобы получить URL-адрес клиента, если он вам неизвестен.
    3. В текстовом поле **Идентификатор** введите URL-адрес клиента. 
    4. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в HackerOne** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 
    1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
    2. Нажмите кнопку **Далее**.
5. Войдите в клиент HackerOne как администратор.
6. В верхнем меню щелкните **Settings**(Параметры).
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 
7. Перейдите к разделу **Authentication** (Аутентификация) и щелкните **Add SAML settings** (Добавить параметры SAML).
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 
8. В диалоговом окне **SAML Settings** (Параметры SAML) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 
    1. Введите имя зарегистрированного домена в поле **Email Domain** (Домен электронной почты).
    2. На классическом портале Azure скопируйте **URL-адрес службы единого входа**и вставьте его в текстовое поле "Single Sign On URL" (URL-адрес единого входа).
    3. Создайте файл в кодировке **Base-64** из загруженного сертификата.  
    
       >[!TIP] 
       >Дополнительные сведения вы можете узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
       >
    4. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте содержимое сертификата в буфер обмена, а затем вставьте его в текстовое поле **X509 Certificate** (Сертификат X.509).
    5. Щелкните **Сохранить**.
9. В диалоговом окне "Authentication Settings" (Параметры аутентификации) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 
    1. Щелкните **Run test**(Выполнить проверку).
    2. Если поле **Status** (Состояние) имеет значение **Last test status: created** (Состояние при последней проверке: создан), обратитесь в службу поддержки HackerOne по адресу [support@hackerone.com](mailto:support@hackerone.com), чтобы запросить проверку своей конфигурации.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
11. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Затем необходимо создать на классическом портале тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 
    1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
    2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
    3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 
    1. Запишите значение поля **Новый пароль**.
    2. Нажмите **Завершено**.   

### <a name="create-a-hackerone-test-user"></a>Создание тестового пользователя HackerOne
Затем нужно создать пользователя Britta Simon в приложении HackerOne. Приложение HackerOne поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь, если он еще не существует, создается при доступе к HackerOne. [Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки HackerOne. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon применять единый вход Azure, предоставив ей доступ к HackerOne.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в HackerOne, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **HackerOne**.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
И, наконец, нужно проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув плитку HackerOne на панели доступа, вы автоматически войдете в приложение HackerOne.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

