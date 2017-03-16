---
title: "Руководство по интеграции Azure Active Directory с BenSelect | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и BenSelect."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffa17478-3ea1-4356-a289-545b5b9a4494
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4682c79f1328af58326f709d94614429c5fd9f7f
ms.openlocfilehash: e9eb8f444597000afae3de7a4cdfc60301bc3cea
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-benselect"></a>Руководство. Интеграция Azure Active Directory с BenSelect
В этом руководстве описано, как интегрировать BenSelect с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением BenSelect дает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к BenSelect.
* Вы можете включить автоматический вход пользователей в BenSelect (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с BenSelect, вам потребуется:

* подписка Azure AD;
* подписка на BenSelect с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
>  

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

* Добавление BenSelect из коллекции.
* Настройка и проверка единого входа Azure AD.

## <a name="adding-benselect-from-the-gallery"></a>Добавление BenSelect из коллекции.
Чтобы настроить интеграцию BenSelect с Azure AD, необходимо добавить BenSelect из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BenSelect из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **BenSelect**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_01.png)
7. В области результатов выберите **BenSelect** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в BenSelect с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BenSelect соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BenSelect.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в BenSelect.

Чтобы настроить и проверить единый вход Azure AD в BenSelect, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BenSelect](#creating-a-benselect-test-user)** требуется для создания в BenSelect пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение BenSelect.

Приложение BenSelect ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно на вкладке "**Атрибут**" приложения. На следующем снимке экрана приведен пример. 

![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_06.png)

**Чтобы настроить единый вход Azure AD в BenSelect, сделайте следующее:**

1. На странице интеграции с приложением **BenSelect** классического портала Azure в верхней части страницы меню щелкните **Атрибуты**.
   
     ![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_07.png)
2. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:
   
   | Имя атрибута | Значение атрибута |
   | --- | --- |
   | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier |extractmailprefix([userprincipalname]) |
  1. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.
   
    ![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_08.png)
  2. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки. 
  3. В списке **Значение атрибута** введите ExtractMailPrefix().
  4. В списке **Почта** введите User.userprincipalname.
  5. Нажмите **Завершено**.
3. В меню вверху щелкните **Быстрый запуск**.
   
    ![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_09.png)
4. На странице **Как пользователи должны входить в BenSelect?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_03.png) 
5. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_04.png) 
   1. В текстовое поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://www.benselect.com/enroll/login.aspx?Path={<tenant name>}`.
   2. Нажмите кнопку **Далее**.
6. На странице **Configure single sign-on at BenSelect** (Настройка единого входа в BenSelect) выполните следующие действия:
   
    ![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_05.png)
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
7. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки BenSelect по адресу [support@selerix.com](mailto:support@selerix.com) и предоставьте следующие сведения:
   * Скачанный сертификат
   * URL-адрес единого входа SAML;
   * URL-адрес выхода; 
   * издатель. 
     
   >[!NOTE]
   >Необходимо отметить, что для этой интеграции требуется алгоритм SHA256 (SHA1 не поддерживается), чтобы настроить единый вход на соответствующем сервере, например app2101 и т. п. 
   > 
8. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Единый вход в Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_05.png)    
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_06.png)   
  1. В текстовом поле **Имя** введите **Britta**. 
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-benselect-tutorial/create_aaduser_08.png) 
   1. Запишите значение поля **Новый пароль**.
   2. Нажмите **Завершено**.   

### <a name="create-an-benselect-test-user"></a>Создание тестового пользователя BenSelect
Цель этого раздела — создать пользователя с именем Britta Simon в BenSelect. Обратитесь в службу поддержки BenSelect, чтобы добавить пользователей в учетную запись BenSelect.

>[!NOTE]
>Чтобы создать пользователя вручную, необходимо обратиться в службу поддержки BenSelect по адресу <mailto:support@selerix.com>.
>  

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к BenSelect.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в BenSelect, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **BenSelect**.
   
    ![Настройка единого входа](./media/active-directory-saas-benselect-tutorial/tutorial_benselect_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент BenSelect на панели доступа, вы автоматически войдете в приложение BenSelect.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-benselect-tutorial/tutorial_general_205.png

