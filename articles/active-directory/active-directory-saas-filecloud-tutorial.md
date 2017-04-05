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
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: b672133e0604e35d6e398fbee4db303f70158111
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Учебник. Интеграция Azure Active Directory с FileCloud
Цель этого учебника — показать, как интегрировать FileCloud с Azure Active Directory (Azure AD).

Интеграция FileCloud с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к FileCloud.
* Вы можете включить автоматический вход пользователей в FileCloud (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с FileCloud, вам потребуется:

* подписка Azure AD;
* подписка FileCloud с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление FileCloud из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-filecloud-from-the-gallery"></a>Добавление FileCloud из коллекции
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

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в FileCloud с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в FileCloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в FileCloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в FileCloud.

Чтобы настроить и проверить единый вход Azure AD в FileCloud, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя FileCloud](#creating-a-filecloud-test-user)** нужно для того, чтобы в FileCloud также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении FileCloud.

**Чтобы настроить единый вход Azure AD в FileCloud, выполните следующие действия.**

1. На классическом портале на странице интеграции с приложением **FileCloud** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в FileCloud?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_03.png)

3. На странице диалогового окна **Настроить параметры приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_04.png)
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.filecloudhosted.com`.
  2. В текстовом поле **Идентификатор** введите `https://<subdomain>.filecloudhosted.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`.
  3. Нажмите кнопку **Далее**.
   
    >[!NOTE]
    >Обратите внимание, что вместо этих значений нужно указать фактический URL-адрес для входа и идентификатор. Чтобы узнать эти адреса, обратитесь к группе поддержки FileCloud: <mailto:support@codelathe.com>.
    >  

4. На странице **Настройка единого входа в FileCloud** выполните указанные ниже действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-filecloud-tutorial/tutorial_filecloud_05.png)
 1. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
 2. Нажмите кнопку **Далее**.

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

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
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
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_06.png) 
 1. В текстовом поле **Имя** введите **Britta**.  
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**. 
 5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_07.png)

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-filecloud-tutorial/create_aaduser_08.png) 
 1. Запишите значение поля **Новый пароль**.  
 2. Нажмите **Завершено**.   

### <a name="create-a-filecloud-test-user"></a>Создание тестового пользователя FileCloud
Цель этого раздела — создать пользователя с именем Britta Simon в FileCloud. FileCloud поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению FileCloud (если он еще не создан). 

>[!NOTE]
>Чтобы создать пользователя вручную, необходимо обратиться к группе поддержки FileCloud. 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к FileCloud.

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

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

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

