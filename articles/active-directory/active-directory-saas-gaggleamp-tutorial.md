---
title: "Учебник. Интеграция Azure Active Directory с GaggleAMP | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в приложении GaggleAMP."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9cc1a4b7-964b-406b-9e0c-05cb1a7c9856
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 3c83745c09514e63cfe686fffd319328b19ee460
ms.openlocfilehash: b70cb12131d97fcf2569f5efd97b87037baa1837
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-gaggleamp"></a>Руководство. Интеграция Azure Active Directory с GaggleAMP
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением GaggleAMP.

Интеграция Azure AD с приложением GaggleAMP обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к приложению GaggleAMP.
* Вы можете включить автоматический вход пользователей в GaggleAMP (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure. 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с GaggleAMP, вам потребуется:

* подписка Azure AD;
* подписка на GaggleAMP с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление GaggleAMP из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-gaggleamp-from-the-gallery"></a>Добавление GaggleAMP из коллекции
Чтобы настроить интеграцию GaggleAMP с Azure AD, необходимо добавить GaggleAMP из коллекции в список управляемых приложений SaaS.

**Чтобы добавить GaggleAMP из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **GaggleAMP**.
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_01.png)
7. В области результатов выберите **GaggleAMP** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_02.png)>

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в GaggleAMP с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в GaggleAMP соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в GaggleAMP.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в GaggleAMP.

Чтобы настроить и проверить единый вход Azure AD в GaggleAMP, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя GaggleAMP](#creating-a-GaggleAMP-test-user)** требуется для создания в GaggleAMP пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении GaggleAMP.

**Чтобы настроить единый вход Azure AD в GaggleAMP, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **GaggleAMP** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в GaggleAMP?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_04.png) 
   1. В текстовом поле "URL-адрес входа" введите URL-адрес, используемый пользователями для входа в приложение GaggleAMP по следующей схеме: **https://secure4.gaggleamp.com**.

    >[!NOTE]
    >Если вам нужен [URL единого входа](mailto:sales@gaggleamp.com) для приложения, обратитесь в **отдел продаж GaggleAMP** .
    >
   2. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в GaggleAMP** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_05.png)   
  1. Щелкните **Загрузить сертификат**и сохраните файл сертификата на свой компьютер. Этот сертификат и URL-адреса метаданных (идентификатор сущности, URL-адрес единого входа и URL-адрес выхода) понадобятся для настройки единого входа на стороне GaggleAMP.
  2. Нажмите кнопку **Далее**.
5. В другом экземпляре браузера перейдите на страницу единого входа SAML, созданную для вас группой поддержки Gaggle (например, *https://accounts.gaggleamp.com/saml_configurations/oXH8sQcP79dOzgFPqrMTyw/edit*).
6. На странице **Единый вход SAML** выполните следующие действия.  
   
    ![Единый вход в GaggleAMP](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_06.png)  
  1. На классическом портале Azure скопируйте **URL-адрес издателя** и вставьте его в текстовое поле **Издатель поставщика удостоверений**.  
  2. На классическом портале Azure скопируйте **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес единого входа поставщика удостоверений**. 
  3. Нажмите кнопку **Сохранить**      
  4. Отправьте скачанный сертификат [групе продаж GaggleAMP](mailto:sales@gaggleamp.com).
5. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
6. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-gaggleamp-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-gaggleamp-test-user"></a>Создание тестового пользователя GaggleAMP
Цель этого раздела — создать в приложении GaggleAMP пользователя с именем Britta Simon. Приложение GaggleAMP поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к GaggleAMP (если он еще не создан). 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к GaggleAMP, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в GaggleAMP, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на портале Azure нажмите **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **GaggleAMP**.
    ![Список Azure](./media/active-directory-saas-gaggleamp-tutorial/tutorial_gaggleamp_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент GaggleAMP на панели доступа, вы автоматически войдете в приложение GaggleAMP.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-gaggleamp-tutorial/tutorial_general_205.png

