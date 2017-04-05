---
title: "Руководство по интеграции Azure Active Directory с Voyance | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Voyance."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 0746f010e8d001a66f8de0a32ea0147774da657b
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Руководство по интеграции Azure Active Directory с Voyance

В этом руководстве описано, как интегрировать Voyance с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Voyance обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Voyance.
- Вы можете включить автоматический вход пользователей в Voyance (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Voyance, вам потребуется:

- подписка Azure AD;
- подписка Voyance с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
>

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Voyance из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-voyance-from-the-gallery"></a>Добавление Voyance из коллекции
Чтобы настроить интеграцию Voyance с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Voyance из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Voyance**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_01.png)

7. В области результатов выберите **Voyance** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_0001.png)


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в Voyance с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Voyance соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Voyance.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве **имени пользователя** в Voyance.

Чтобы настроить и проверить единый вход Azure AD в Voyance, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Voyance](#creating-a-voyance-test-user)** требуется для создания в Voyance пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Voyance.


**Чтобы настроить единый вход Azure AD в Voyance, выполните следующие действия:**

1. На классическом портале на странице интеграции с приложением **Voyance** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа][6]

2. На странице **Как пользователи должны входить в Voyance?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
 
    ![Настройка единого входа](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_02.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то на странице диалогового окна **Настроить параметры приложения** выполните следующие действия и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_03.png)
  1. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.nyansa.com`.
  2. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.nyansa.com/saml/create/`.
  3. Нажмите кнопку **Далее**.

4. Если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**, то на странице диалогового окна **Настроить параметры приложения** щелкните **Показать дополнительные параметры (необязательно)**, а затем введите **URL-адрес для входа** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_04.png)
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.nyansa.com/`.
  2. Нажмите кнопку **Далее**.

      >[!NOTE]
      >Необходимо обновить фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки Voyance](emaiLto:support@nyansa.com).
      >

5. На странице **Настройка единого входа в Voyance** щелкните **Скачать сертификат** и сохраните файл сертификата на своем компьютере.

    ![Настройка единого входа](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_05.png) 

6. В другом окне веб-браузера войдите в свой клиент Voyance с правами администратора.

7. Перейдите к правому верхнему углу панели навигации и щелкните раскрывающийся список **Acme University**.
    
    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_001.png) 

8. Щелкните **Admin Settings** (Параметры администрирования).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_002.png)

9. Щелкните вкладку **User Access** (Доступ пользователей).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_003.png)

10. Чтобы настроить Azure AD в качестве поставщика удостоверений (IdP) с использованием SAML 2.0, нажмите кнопку **SSO is disabled** (Единый вход отключен).

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_004.png)

11. Перейдите в раздел **SAML v2** и выполните описанные ниже действия.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_005.png)
 1. Щелкните **Включено**.
 2. В текстовом поле **IdP Login URL** (URL-адрес входа IdP) введите значение **URL-адреса единого входа SAML** из мастера настройки приложения в Azure AD.
 3. Откройте сертификат в кодировке Base64 с помощью блокнота, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **IdP Cert** (Сертификат IdP).
 4. Щелкните **Сохранить**.

12. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.

    ![единого входа Azure AD][10]

13. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
  
    ![единого входа Azure AD][11]


### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_05.png) 
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
 3. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_06.png) 
 1. В текстовом поле **Имя** введите **Britta**.  
 2. В текстовом поле **Фамилия** введите **Simon**.
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
 4. В списке **Роль** выберите **Пользователь**.
 5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-voyance-tutorial/create_aaduser_08.png) 
 1. Запишите значение поля **Новый пароль**.
 2. Нажмите **Завершено**.   

### <a name="create-a-voyance-test-user"></a>Создание тестового пользователя Voyance

Цель этого раздела — создать пользователя с именем Britta Simon в приложении Voyance. Приложение Voyance поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Voyance (если он еще не создан).

>[!NOTE]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки Voyance](emaiLto:support@nyansa.com).
>

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Voyance.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Voyance, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Voyance**.

    ![Настройка единого входа](./media/active-directory-saas-voyance-tutorial/tutorial_voyance_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203] 

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
    
    ![Назначение пользователя][205]


### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Voyance на панели доступа, вы автоматически войдете в приложение Voyance.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-voyance-tutorial/tutorial_general_205.png

