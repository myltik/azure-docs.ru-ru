---
title: "Руководство. Интеграция Azure Active Directory с приложениями Splunk Enterprise и Splunk Cloud | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Splunk Enterprise и Splunk Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 44bf5ae5df09b564b4b3ade495ed3aa4e52676e7
ms.openlocfilehash: 7dcb70766c132ec00ee9199ea0152cd07aa50c8f


---

# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Руководство. Интеграция Azure Active Directory с приложениями Splunk Enterprise и Splunk Cloud

В этом руководстве описано, как интегрировать Splunk Enterprise и Splunk Cloud с Azure Active Directory (Azure AD).

Интеграция Splunk Enterprise и Splunk Cloud с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Splunk Enterprise и Splunk Cloud.
- Вы можете включить автоматический вход пользователей в Splunk Enterprise и Splunk Cloud (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложениями Splunk Enterprise и Splunk Cloud, вам потребуется следующее:

- подписка Azure AD;
- подписка Splunk Enterprise или Splunk Cloud с поддержкой единого входа.


>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Splunk Enterprise и Splunk Cloud из коллекции.
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Добавление Splunk Enterprise и Splunk Cloud из коллекции
Чтобы настроить интеграцию Splunk Enterprise и Splunk Cloud с Azure AD, необходимо добавить Splunk Enterprise и Splunk Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Splunk Enterprise и Splunk Cloud из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.

    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Приложения][4]

6. В поле поиска введите **Splunk Enterprise или Splunk Cloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_01.png)

7. В области результатов выберите **Splunk Enterprise и Splunk Cloud**, а затем нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описывается настройка и проверка единого входа Azure AD в Splunk Enterprise и Splunk Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо указать, какой пользователь в Splunk Enterprise и Splunk Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Splunk Enterprise и Splunk Cloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Splunk Enterprise и Splunk Cloud.

Чтобы настроить и проверить единый вход Azure AD в Splunk Enterprise и Splunk Cloud, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Splunk Enterprise и Splunk Cloud](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)** требуется для создания пользователя Britta Simon в Splunk Enterprise и Splunk Cloud, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Splunk Enterprise и Splunk Cloud.


**Чтобы настроить единый вход Azure AD в Splunk Enterprise и Splunk Cloud, выполните следующие действия.**

1. На странице интеграции с приложениями **Splunk Enterprise и Splunk Cloud** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
     
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Splunk Enterprise и Splunk Cloud** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_04.png) 

    а. В текстовом поле **URL-адрес входа** введите URL-адрес, с помощью которого пользователи входят в приложение Splunk Enterprise и Splunk Cloud, в следующем формате: `https://<splunkserverUrl>/en-US/app/launcher/home`
    
    b. В текстовом поле **Идентификатор** введите URL-адрес сервера Splunk Server.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<splunkserver>/saml/acs`

    d. Щелкните **Далее**
 
4. На странице **Настройка единого входа в Splunk Enterprise и Splunk Cloud** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_05.png)

    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.

    b. Нажмите кнопку **Далее**.


5. Чтобы настроить единый вход для своего приложения, обратитесь в службу поддержки Splunk Enterprise и Splunk Cloud и укажите следующие сведения:

    а. Скачанный файл с **метаданными федерации**.

6. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]

7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
 
    ![единого входа Azure AD][11]


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.


![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_05.png) 

    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».

    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.

    c. Нажмите кнопку **Далее**.

6.  На странице диалогового окна **Профиль пользователя** выполните следующие действия. ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_06.png) 

    а. В текстовом поле **Имя** введите **Britta**.  

    b. В текстовом поле **Фамилия** введите **Simon**.

    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.

    d. В списке **Роль** выберите **Пользователь**.

    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_08.png) 

    а. Запишите значение поля **Новый пароль**.

    b. Нажмите **Завершено**.   



### <a name="creating-an-splunk-enterprise-and-splunk-cloud-test-user"></a>Создание тестового пользователя Splunk Enterprise и Splunk Cloud

В этом разделе описано, как создать пользователя Britta Simon в Splunk Enterprise и Splunk Cloud. Чтобы добавить пользователей в Splunk Enterprise и Splunk Cloud, обратитесь в службу поддержки платформы Splunk Enterprise и Splunk Cloud.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Splunk Enterprise и Splunk Cloud.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в Splunk Enterprise и Splunk Cloud, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Splunk Enterprise и Splunk Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.

    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.

    ![Назначение пользователя][205]


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Splunk Enterprise и Splunk Cloud на панели доступа, вы автоматически войдете в приложение Splunk Enterprise и Splunk Cloud.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


