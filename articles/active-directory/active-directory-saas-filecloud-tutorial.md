---
title: "Учебник. Интеграция Azure Active Directory с FileCloud | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и FileCloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f39f0ddd-b504-4562-971f-77b88d1e75fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 69473976eb0a9b6a0e9aa929581c0a7cf0c8d188
ms.openlocfilehash: 49779d5d15e81a50e32b87255152d996600d23eb
ms.lasthandoff: 12/14/2016


---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Учебник. Интеграция Azure Active Directory с FileCloud
Цель этого учебника — показать, как интегрировать FileCloud с Azure Active Directory (Azure AD).

Интеграция FileCloud с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к FileCloud.
* Вы можете включить автоматический вход пользователей в FileCloud (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с FileCloud, вам потребуется:

* подписка Azure AD;
* подписка FileCloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление FileCloud из коллекции
2. Настройка и проверка единого входа в Azure AD.

## <a name="adding-filecloud-from-the-gallery"></a>Добавление FileCloud из коллекции
Чтобы настроить интеграцию FileCloud с Azure AD, необходимо добавить FileCloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить FileCloud из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **FileCloud**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_01.png)

7. В области результатов выберите **FileCloud** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в FileCloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в FileCloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в FileCloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в FileCloud.

Чтобы настроить и проверить единый вход Azure AD в FileCloud, выполните следующие стандартные действия.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя FileCloud](#creating-a-filecloud-test-user)** нужно для того, чтобы в FileCloud также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении FileCloud.

**Чтобы настроить единый вход Azure AD в FileCloud, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **FileCloud** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в FileCloud?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_03.png)

3. На странице диалогового окна **Настроить параметры приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_04.png)
   
    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.filecloudhosted.com`.
   
    b. В текстовом поле **Идентификатор** введите `https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`.
   
    c. Щелкните **Далее**
   
    > [!NOTE]
    > Обратите внимание, что вместо этих значений нужно указать фактический URL-адрес для входа и идентификатор. Чтобы узнать эти адреса, обратитесь к группе поддержки FileCloud: <mailto:support@codelathe.com>.
    > 
    > 

4. На странице **Настройка единого входа в FileCloud** выполните указанные ниже действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_05.png)
   
    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
   
    b. Нажмите кнопку **Далее**.

5. В другом окне веб-браузера войдите в свой клиент FileCloud с правами администратора.

6. В левой панели навигации щелкните **Settings**(Параметры). 
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_000.png)

7. Щелкните вкладку **SSO** (Единый вход) в разделе "Settings" (Параметры). 
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_001.png)

8. На панели **Single Sign On (SSO) Settings** (Параметры единого входа) для параметра **Default SSO Type** (Тип единого входа по умолчанию) выберите значение **SAML**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_002.png)

9. В текстовое поле **IdP End Point URL** (URL-адрес конечной точки IdP) на панели **SAML Settings** (Параметры SAML) вставьте значение **идентификатора сущности** из мастера настройки приложения в Azure AD.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_003.png)

10. Откройте скачанный файл метаданных в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте в текстовое поле **IdP Meta Data** (Метаданные IdP) на панели **SAML Settings** (Параметры SAML).
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_004.png)

11. Нажмите кнопку **Сохранить** .

12. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

13. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_09.png)

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_03.png)

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_04.png)

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_05.png)
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_06.png)
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-a-filecloud-test-user"></a>Создание тестового пользователя FileCloud
Цель этого раздела — создать пользователя с именем Britta Simon в FileCloud. FileCloud поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению FileCloud (если он еще не создан). 

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться к группе поддержки FileCloud.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к FileCloud.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в FileCloud, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201]

2. Из списка приложений выберите **FileCloud**.
   
    ![Настройка единого входа](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_50.png)

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "FileCloud" на панели доступа, вы автоматически войдете в приложение FileCloud.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-filecloud-tutorial/tutorial_general_205.png

