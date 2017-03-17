---
title: "Учебник. Интеграция Azure Active Directory с Front | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Front."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7c4faaba6db10f6803236a5d72eed375e2c39d46
ms.openlocfilehash: b0ecc19b62e7620b4c37c4d9f702238dcb8c44cc
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Учебник. Интеграция Azure Active Directory с Front
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Front.

Интеграция Azure AD с приложением Front обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Front.
* Вы можете включить автоматический вход пользователей во Front (единый вход) с использованием их учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Front, вам потребуется:

* подписка Azure AD;
* подписка на Front с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель данного руководства — научить вас проверять единый вход Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Front из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-front-from-the-gallery"></a>Добавление Front из коллекции
Чтобы настроить интеграцию Front с Azure AD, необходимо добавить Front из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Front из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Front**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)
7. В области результатов выберите **Front** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Выбор приложения в коллекции](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD во Front с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь во Front соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Front.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Front.

Чтобы настроить и проверить единый вход Azure AD во Front, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Front](#creating-a-front-test-user)** требуется для создания пользователя Britta Simon в Front, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-sso"></a>Настройка единого входа Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Front.

**Чтобы настроить единый вход Azure AD во Front, выполните следующие действия:**

1. На классическом портале на странице интеграции с приложением **Front** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Front** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)
3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)
  1. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.frontapp.com`.
  2. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.frontapp.com/sso/saml/callback`.
  3. Нажмите кнопку **Далее**.
4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)
   
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.frontapp.com`.
  2. Нажмите кнопку **Далее**.
   
   >[!NOTE]
   >Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Для получения этих значений см. **шаг 12** или свяжитесь с Front по адресу [support@frontapp.com](emailTo:support@frontapp.com).
   >  
5. На странице **Настройка единого входа в Front** выполните следующие действия и нажмите кнопку **Далее**.
   
 ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_06.png) 
 1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
 2. Нажмите кнопку **Далее**.
6. Войдите в клиент Front с правами администратора.
7. Последовательно выберите пункты **Settings (Параметры) (значок шестеренки в нижней части левой боковой панели) > Preferences (Предпочтения)**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)
8. Щелкните ссылку **Single Sign On** (Единый вход).
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)
9. В раскрывающемся списке **Single Sign On** (Единый вход) выберите **SAML**.
   
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)
10. В текстовое поле **Entry Point** (Точка входа) введите значение **URL-адрес службы единого входа** из мастера настройки приложения Azure AD.
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)
11. Скопируйте содержимое скачанного файла сертификата и вставьте его в текстовое поле **Signing certificate** (Сертификат для подписи).
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)
12. Подтвердите, что эти URL-адреса соответствуют конфигурации из шага 3.
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)
13. Нажмите кнопку **Сохранить** .
14. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]
15. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_06.png) 
 1. В текстовом поле **Имя** введите **Britta**.   
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**. 
 5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-front-tutorial/create_aaduser_08.png) 
 1. Запишите значение поля **Новый пароль**. 
 2. Нажмите **Завершено**.   

### <a name="create-a-front-test-user"></a>Создание тестового пользователя Front
Цель этого раздела — создать пользователя с именем Britta Simon в Front. Обратитесь в службу поддержки Front, чтобы добавить пользователей в учетную запись Front.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Front, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Front, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **Front**.
   
    ![Настройка единого входа](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Front на панели доступа, вы автоматически войдете в приложение Front.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png

