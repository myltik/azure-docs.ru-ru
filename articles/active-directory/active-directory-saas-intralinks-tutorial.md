---
title: "Учебник. Интеграция Azure Active Directory с Intralinks | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Intralinks."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: 2112f4b129ff8735955d440341174a44a6a1469c
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Руководство. Интеграция Azure Active Directory с Intralinks
В этом руководстве описано, как интегрировать Intralinks с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Intralinks обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Intralinks.
* Вы можете включить автоматический вход пользователей в Intralinks (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Intralinks, вам потребуется:

* подписка Azure AD;
* подписка на Intralinks с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Intralinks из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="add-intralinks-from-the-gallery"></a>Добавление Intralinks из коллекции
Чтобы настроить интеграцию Intralinks с Azure AD, необходимо добавить Intralinks из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Intralinks из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Intralinks**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_01.png)
7. В области результатов выберите **Intralinks** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_02.png)

## <a name="configure-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Intralinks с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Intralinks соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Intralinks.

>[!NOTE]
>Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Intralinks.
>

Чтобы настроить и проверить единый вход Azure AD в Intralinks, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Intralinks](#creating-an-intralinks-test-user)** требуется для создания пользователя Britta Simon в Intralinks, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Intralinks.

**Чтобы настроить единый вход Azure AD в Intralinks, выполните следующие действия.**

1. На странице интеграции с приложением **Intralinks** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Intralinks?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_03.png) 
3. На странице диалогового окна **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_04.png) 
   
    1. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение Intralinks, в следующем формате: **https://\<название_компании\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<идентификатор клиента Azure AD\>/**.
    2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Intralinks** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_05.png)
   
    1. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
    2. Нажмите кнопку **Далее**.
5. Чтобы получить данные единого входа, настроенные для вашего приложения, напишите в службу поддержки Intralinks по электронной почте, прикрепив скачанный файл сертификата.
6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

* В списке пользователей выберите **Britta Simon**.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_05.png) 
   
    1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации». 
    2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
    3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.   
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_08.png) 
   
    1. Запишите значение поля **Новый пароль**.
    2. Нажмите **Завершено**.   

### <a name="create-an-intralinks-test-user"></a>Создание тестового пользователя Intralinks
В этом разделе описано, как создать пользователя Britta Simon в приложении Intralinks. Обратитесь в службу поддержки Intralinks, чтобы добавить пользователей в платформу Intralinks.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Intralinks.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Intralinks, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Intralinks**.
   
    ![Настройка единого входа](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="add-intralinks-via-or-elite-application"></a>Добавление приложения Intralinks VIA или Elite
Для всех приложений Intralinks используется одна платформа удостоверений единого входа, за исключением приложения Deal Nexus. Поэтому, если вы планируете использовать любое другое приложение Intralinks, сначала необходимо настроить единый вход для одного основного приложения Intralinks с помощью процедуры, описанной выше.

После этого вы можете выполнить описанные ниже действия, чтобы добавить другое приложение Intralinks в клиент, который может использовать это основное приложение для единого входа. 

>[!NOTE]
>Эта функция доступна только для клиентов SKU Azure AD уровня "Премиум" и недоступна для клиентов SKU уровня "Бесплатный" или "Базовый".
> 

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В области слева щелкните вкладку **Настраиваемая**.
   
    ![Добавление приложения Intralinks VIA или Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_51.png)
7. Присвойте приложению соответствующее имя, например **Intralinks Elite**, а затем нажмите кнопку "Готово".
8. Нажмите кнопку **Настройка единого входа**.
9. Выберите параметр **Existing Single Sign On** (Существующий единый вход).
   
    ![Добавление приложения Intralinks VIA или Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_52.png)
10. Получите от команды Intralinks URL-адрес единого входа, инициированный поставщиком услуг, для другого приложения Intralinks. Затем введите этот URL-адрес, как показано ниже. 
    
    ![Добавление приложения Intralinks VIA или Elite](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_53.png)
    
    * В текстовом поле "URL-адрес для входа" введите URL-адрес, используемый для входа в приложение Intralinks, в следующем формате: **https://\<название_компании\>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/\<идентификатор_клиента_Azure_AD\>/**.
11. Нажмите кнопку **Далее**.
12. Назначьте приложения пользователям или группам, как показано в разделе **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)**.

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Intralinks на панели доступа, вы автоматически войдете в приложение Intralinks.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_205.png

