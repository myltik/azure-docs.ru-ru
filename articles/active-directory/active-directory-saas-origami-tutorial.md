---
title: "Руководство по интеграции Azure Active Directory с Origami | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Origami."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 53251465d5153002fbdef6ac69ecd3979bb19ec3
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Руководство. Интеграция Azure Active Directory с Origami
В этом учебнике описано, как интегрировать Origami с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Origami обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Origami.
* Вы можете включить автоматический вход пользователей в Origami (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Origami, вам потребуется:

* подписка Azure AD;
* подписка на Origami с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Origami из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-origami-from-the-gallery"></a>Добавление Origami из коллекции
Чтобы настроить интеграцию Origami с Azure AD, необходимо добавить Origami из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Origami из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Origami**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_01.png)
7. В области результатов выберите **Origami** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе можно настроить и проверить единый вход Azure AD в Origami с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Origami соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Origami.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Origami.

Чтобы настроить и проверить единый вход Azure AD в Origami, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Origami](#creating-a-origami-test-user)** требуется для создания в Origami пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Origami.

**Чтобы настроить единый вход Azure AD в Origami, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **Origami** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Origami?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_04.png)
  1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Origami, по следующей схеме: **https://live.origamirisk.com/origami/account/login?account=\<название_организации\>**. 
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Origami** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_05.png)
  1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
  2. Нажмите кнопку **Далее**.
5. Войдите в систему, используя учетную запись Origami с правами администратора.
6. В верхнем меню щелкните **Администратор**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
7. На странице диалогового окна "Настройка единого входа" сделайте следующее.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/123.png)
  1. Выберите пункт **Включить единый вход**.
  2. На классическом портале Azure скопируйте значение поля **URL-адрес единого входа SAML**, а затем вставьте его в текстовое поле**Identity Provider's Sign-in Page URL** (URL-адрес страницы входа поставщика удостоверений).
  3. На классическом портале Azure скопируйте значение поля **URL-адрес службы единого выхода**, а затем вставьте его в текстовое поле **Identity Provider's Sign-out Page URL** (URL-адрес страницы выхода поставщика удостоверений).
  4. Нажмите кнопку **Обзор**, чтобы отправить файл метаданных, скачанный с классического портала Azure.
  5. Нажмите кнопку **Сохранить изменения**.
8. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.    
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_08.png)  
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-an-origami-test-user"></a>Создание тестового пользователя Origami
В этом разделе описано, как создать пользователя Britta Simon в приложении Origami. 

1. Войдите в систему, используя учетную запись Origami с правами администратора.
2. В верхнем меню щелкните **Администратор**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
3. В диалоговом окне **Users and Security** (Пользователи и безопасность) нажмите кнопку **Users** (Пользователи).
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)
4. Нажмите кнопку **Add New User**(Добавить нового пользователя).
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)
5. В диалоговом окне добавления нового пользователя выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)
  1. В текстовом поле **User Name** (Имя пользователя) введите имя пользователя для Britta Simon в классическом портале Azure.
  2. В текстовом поле **Password** (Пароль) введите пароль.
  3. В текстовом поле **подтверждения пароля** введите пароль еще раз.
  4. В текстовом поле **Имя** введите **Britta**.
  5. В текстовом поле **Фамилия** введите **Simon**.
  6. Щелкните **Сохранить**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)
6. Назначьте **роли пользователя** и уровень **клиентского доступа** для пользователя. 
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Origami, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Origami, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Origami**.
   
    ![Настройка единого входа](./media/active-directory-saas-origami-tutorial/tutorial_origami_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Origami на панели доступа, вы автоматически войдете в приложение Origami.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-origami-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-origami-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-origami-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-origami-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-origami-tutorial/tutorial_general_205.png

