---
title: "Руководство по интеграции Azure Active Directory с Proofpoint on Demand | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в Proofpoint on Demand."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7aa746b8c6f4b747a7dca0e9bf75e61b2707b7e1


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Руководство. Интеграция Azure Active Directory с Proofpoint on Demand
В этом руководстве описано, как интегрировать Proofpoint on Demand с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Proofpoint on Demand обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Proofpoint on Demand.
* Вы можете включить автоматический вход пользователей в Proofpoint on Demand (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Proofpoint on Demand, вам потребуется:

* подписка Azure AD;
* подписка с поддержкой единого входа Proofpoint on Demand.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

* Не используйте рабочую среду без необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Proofpoint on Demand из коллекции.
2. Настройка и проверка единого входа в Azure AD.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Добавление Proofpoint on Demand из коллекции
Чтобы настроить интеграцию приложения Proofpoint on Demand с Azure AD, необходимо добавить это приложение из коллекции в список управляемых приложений SaaS.

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Значок Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога щелкните **Приложения** в верхнем меню.
   
    ![Элемент "Приложения" в меню][2]
4. В нижней части страницы нажмите кнопку **Добавить**.
   
    ![Кнопка "Добавить"][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Выбор элемента "Добавить приложение из коллекции"][4]
6. В поле поиска введите **Proofpoint on Demand**.
   
    ![Поле поиска, в которое нужно ввести Proofpoint on Demand](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. В области результатов выберите **Proofpoint on Demand** и щелкните **Завершить**, чтобы добавить приложение.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в Proofpoint on Demand с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в Proofpoint on Demand соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Proofpoint on Demand.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Proofpoint on Demand.

Чтобы настроить и проверить единый вход Azure AD в Proofpoint on Demand, выполните следующие действия.

1. [Настройте единый вход в Azure AD](#configuring-azure-ad-single-sign-on), чтобы пользователи могли использовать эту функцию.
2. [Создайте тестового пользователя Azure AD](#creating-an-azure-ad-test-user) для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создайте тестового пользователя Proofpoint on Demand](#creating-a-proofpoint-ondemand-test-user), чтобы создать пользователя Britta Simon в Proofpoint on Demand, связанного с соответствующим представлением в Azure AD.
4. [Назначьте тестового пользователя Azure AD](#assigning-the-azure-ad-test-user), чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. [Проверьте единый вход](#testing-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Proofpoint on Demand.

1. На классическом портале на странице интеграции с приложением **Proofpoint on Demand** нажмите кнопку **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Кнопка "Настроить единый вход"][6]
2. На странице **Как пользователи должны входить в Proofpoint on Demand?** выберите **Единый вход Microsoft Azure AD** и щелкните **Далее**.
   
    ![Переключатель "Единый вход Microsoft Azure AD"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. На странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Страница "Настроить параметры приложения" с заполненными полями](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   
    а. В поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Proofpoint on Demand. Используйте следующий формат: **https://\<имя_узла\>.pphosted.com/ppssamlsp_hostname**.
   
    b. В поле **Идентификатор** введите URL-адрес по следующей схеме: **https://\<имя_узла/>.pphosted.com/ppssamlsp**.
   
    c. В поле **URL-адрес ответа** введите URL-адрес по следующей схеме: **https://\<имя_узла/>.pphosted.com:portnumber/v1/samlauth/samlconsumer**.
   
    d. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Proofpoint on Demand** выполните следующие действия.
   
    ![Страница "Настройка единого входа в Proofpoint on Demand" с кнопкой "Скачать сертификат"](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. Чтобы настроить единый вход для своего приложения, обратитесь к группе поддержки Proofpoint on Demand и предоставьте следующие сведения:
   
    • скачанный сертификат;
   
    • идентификатор сущности;
   
    • URL-адрес единого входа SAML.
6. На классическом портале выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![Флажок, подтверждающий настройку единого входа][10]
7. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
   
    ![Страница подтверждения][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Тестовый пользователь в списке пользователей][20]

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Значок Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху щелкните **Пользователи**.
   
    ![Элемент "Пользователи" в меню](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Кнопка "Добавить пользователя"](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. На странице **Тип учетной записи пользователя** выполните следующие действия.  ![Страница "Тип учетной записи пользователя" с заполненными полями](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)
   
    а. В поле **Тип пользователя** выберите **Новый пользователь в вашей организации**.
   
    b. В поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице **Профиль пользователя** выполните следующие действия. ![Страница "Профиль пользователя" с заполненными полями](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)
   
   а. В поле **Имя** введите **Britta**.  
   
   b. В поле **Фамилия** введите **Simon**.
   
   c. В поле **Отображаемое имя** введите **Britta Simon**.
   
   d. В списке **Роль** выберите **Пользователь**.
   
   д. Нажмите кнопку **Далее**.
7. На странице **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Кнопка для создания временного пароля](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. На странице **Получить временный пароль** выполните следующие действия.
   
    ![Страница "Получить временный пароль" с информацией о пароле](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="create-a-proofpoint-on-demand-test-user"></a>Создание тестового пользователя Proofpoint on Demand
В этом разделе описано, как создать пользователя Britta Simon в Proofpoint on Demand. Обратитесь в службу поддержки Proofpoint on Demand, чтобы добавить пользователей на платформу Proofpoint on Demand.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Proofpoint on Demand.

![Сведения о пользователе, где указано, что доступ включен напрямую][200]

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Элемент "Приложения" в меню][201]
2. В списке приложений выберите **Proofpoint on Demand**.
   
    ![Список приложений с выбранным Proofpoint on Demand](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Элемент "Пользователи" в меню][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Кнопка "Назначить"][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку **Proofpoint on Demand** на панели доступа, вы автоматически войдете в приложение Proofpoint on Demand.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


