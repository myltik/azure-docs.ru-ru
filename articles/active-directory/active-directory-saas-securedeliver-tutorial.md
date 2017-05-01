---
title: "Руководство по интеграции Azure Active Directory с Novatus | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в SECURE DELIVER."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: fccd5668-fe6f-4e6d-a9ce-ba4f321c33d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 72aaf3835604b1e26f1a3d79822c844be12f0627
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-secure-deliver"></a>Руководство. Интеграция Azure Active Directory с SECURE DELIVER
В данном руководстве рассказывается о том, как интегрировать SECURE DELIVER с Azure Active Directory (Azure AD).  

Интеграция SECURE DELIVER с Azure AD имеет следующие преимущества.

* С помощью Azure AD вы можете управлять доступом к SECURE DELIVER.
* Вы можете включить автоматический вход пользователей в SECURE DELIVER (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с SECURE DELIVER, вам потребуется следующее.

* Подписка Azure
* Подписка SECURE DELIVER с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.  

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SECURE DELIVER из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-secure-deliver-from-the-gallery"></a>Добавление SECURE DELIVER из коллекции
Чтобы настроить интеграцию SECURE DELIVER с Azure AD, необходимо добавить SECURE DELIVER из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SECURE DELIVER из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **SECURE DELIVER**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_01.png)

7. В области результатов выберите **SECURE DELIVER** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Имя и логотип приложения в коллекции](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в SECURE DELIVER с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в SECURE DELIVER соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SECURE DELIVER.  

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SECURE DELIVER.

Чтобы настроить и проверить единый вход Azure AD в SECURE DELIVER, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя в SECURE DELIVER](#creating-a-secure-deliver-test-user)** требуется для создания пользователя Britta Simon в SECURE DELIVER, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении SECURE DELIVER.

**Чтобы настроить единый вход Azure AD в SECURE DELIVER, выполните следующие действия.**

1. На классическом портале Azure на странице интеграции с приложением **SECURE DELIVER** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в SECURE DELIVER?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_03.png) 

3. На странице диалогового окна **Настроить параметры приложения** выполните описанные ниже действия, после чего нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_04.png) 
   1. В текстовом поле **URL-адрес входа** введите URL-адрес для входа в приложение SECURE DELIVER, используя следующую схему: **https://i-securedeliver.jp/sd/\<название_компании\>/jsf/login/sso**.
   2. Обратитесь в службу поддержки SECURE DELIVER по адресу [iw-sd-support@fujifilm.com](mailto:iw-sd-support@fujifilm.com) , чтобы получить URL-адрес клиента, если он вам неизвестен.
   3. В текстовом поле **Идентификатор** введите URL-адрес клиента. 
   4. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в SECURE DELIVER** выполните приведенные ниже действия, после чего нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_05.png) 
   1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   2. Нажмите кнопку **Далее**.

5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки SECURE DELIVER по адресу [iw-sd-support@fujifilm.com](mailto:iw-sd-support@fujifilm.com) и предоставьте следующие сведения:
   
   * Скачанный файл сертификата
   * **идентификатор сущности**
   * **URL-адрес службы единого входа**;
   * **URL-адрес службы единого выхода**

6. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![единого входа Azure AD][11]

###<a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_06.png)  
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.  
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-securedeliver-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-secure-deliver-test-user"></a>Создание тестового пользователя в SECURE DELIVER
Цель этого раздела — создать в приложении SECURE DELIVER пользователя с именем Britta Simon. Обратитесь в службу поддержки SECURE DELIVER, чтобы добавить пользователей в учетную запись SECURE DELIVER.

>[!NOTE]
>Чтобы создать пользователя вручную, обратитесь в службу поддержки SECURE DELIVER.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к SECURE DELIVER, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SECURE DELIVER, сделайте следующее.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **SECURE DELIVER**.
   
    ![Настройка единого входа](./media/active-directory-saas-securedeliver-tutorial/tutorial_securedeliver_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув плитку SECURE DELIVER на панели доступа, вы автоматически войдете в приложение SECURE DELIVER.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-securedeliver-tutorial/tutorial_general_205.png

