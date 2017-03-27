---
title: "Руководство по интеграции Azure Active Directory с приложением SAP HANA Cloud Platform Identity Authentication | Документация Майкрософт"
description: "Сведения о настройке единого входа между Azure Active Directory и SAP HANA Cloud Platform Identity Authentication."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 87c3b66a8789254e962ccfd7fc1eb7842c0db638
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>Руководство по интеграции Azure Active Directory с приложением SAP HANA Cloud Platform Identity Authentication

В этом руководстве описано, как интегрировать приложение SAP HANA Cloud Platform Identity Authentication с Azure Active Directory (Azure AD). Приложение SAP HANA Cloud Platform Identity Authentication используется в качестве поставщика удостоверений прокси-сервера для доступа к приложениям SAP с использованием Azure AD в качестве главного поставщика удостоверений.

Интеграция приложения SAP HANA Cloud Platform Identity Authentication с Azure AD обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к приложению SAP;
- вы можете включить автоматический вход пользователей в приложения SAP (единый вход) с использованием учетной записи Azure AD;
- Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением SAP HANA Cloud Platform Identity Authentication, вам потребуются:

- подписка Azure AD;
- подписка на **SAP HANA Cloud Platform Identity Authentication** с поддержкой единого входа.


>[!NOTE] 
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).



## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление приложения SAP HANA Cloud Platform Identity Authentication из коллекции.
2. Настройка и проверка единого входа в Azure AD.

Прежде чем углубиться в технические сведения, очень важно разобраться в основных понятиях, которые здесь будут представлены. Федерация SAP HANA Cloud Platform Identity Authentication и Azure Active Directory позволяет реализовать единый вход между приложениями и службами, защищенными с помощью AAD (выступающего в качестве поставщика удостоверений), и приложениями и службами SAP, защищенными с помощью SAP HANA Cloud Platform Identity Authentication.

В настоящее время приложение SAP HANA Cloud Platform Identity Authentication выступает в качестве поставщика удостоверений прокси-сервера для приложений SAP. В свою очередь, Azure Active Directory выступает ведущим поставщиком удостоверений. Этот пример представлен на схеме ниже.    

![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/architecture-01.png)

При такой конфигурации клиент SAP HANA Cloud Platform Identity Authentication будет настроен как надежное приложение в Azure Active Directory. 

Все приложения и службы SAP, которые необходимо защитить таким образом, впоследствии настраиваются в консоли управления SAP HANA Cloud Platform Identity Authentication.

Разобраться с этим очень важно! Это означает, что авторизация для предоставления доступа к приложениям и службам SAP для данной установки должна выполняться в приложении SAP HANA Cloud Platform Identity Authentication (в отличие от настройки авторизации в Azure Active Directory).

Настроив SAP HANA Cloud Platform Identity Authentication как приложение в магазине Azure Active Directory, вам не нужно будет настраивать отдельные утверждения или утверждения SAML и преобразования, необходимые для создания действительного маркера проверки подлинности для приложений SAP.

>[!NOTE] 
>В настоящее время единый вход для интернет-решений был проверен только обеими сторонами. Потоки, необходимые для связи "приложение — API" и "API — API", работают, но они еще не проверялись. Они будут проверены в ходе последующих действий. После успешной проверки документация будет обновлена соответствующим образом.

## <a name="adding-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>Добавление приложения SAP HANA Cloud Platform Identity Authentication из коллекции
Для настройки интеграции SAP HANA Cloud Platform Identity Authentication с Azure AD необходимо добавить данное приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP HANA Cloud Platform Identity Authentication из коллекции, сделайте следующее:**

1. На [**портале управления Azure**](https://portal.azure.com) в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **SAP HANA Cloud Platform Identity Authentication**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_01.png)

5. На панели результатов выберите **SAP HANA Cloud Platform Identity Authentication**, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в SAP HANA Cloud Platform Identity Authentication с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход Azure AD необходимо знать, какой пользователь в SAP HANA Cloud Platform Identity Authentication соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP HANA Cloud Platform Identity Authentication.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP HANA Cloud Platform Identity Authentication.

Чтобы настроить и проверить единый вход Azure AD в SAP HANA Cloud Platform Identity Authentication, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP HANA Cloud Platform Identity Authentication](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user)** требуется для создания пользователя Britta Simon в SAP HANA Cloud Platform Identity Authentication, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении SAP HANA Cloud Platform Identity Authentication.

Приложение SAP HANA Cloud Platform Identity Authentication ожидает утверждений SAML в определенном формате. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_03.png)

**Чтобы настроить единый вход Azure AD в приложении SAP HANA Cloud Platform Identity Authentication, сделайте следующее:**

1. На портале управления Azure на странице интеграции приложения **SAP HANA Cloud Platform Identity Authentication** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа][5]

3. В диалоговом окне **Единый вход** в разделе **Атрибуты пользователя** укажите, ожидает ли приложение SAP атрибут, например firstName. В диалоговом окне "Атрибуты токена SAML" добавьте атрибут firstName.

    а. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа][6]

    ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_05.png)
    
    b. В текстовом поле **Имя атрибута** введите firstName.

    c. В списке **Значение атрибута** выберите user.givenname.
    
    d. Нажмите кнопку **ОК**.


4. В разделе **Домены и URL-адреса приложения SAP HANA Cloud Platform Identity Authentication** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_06.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес входа для приложения SAP.

    b. В текстовом поле **Идентификатор** введите значение в следующем формате: `<entity-id>.accounts.ondemand.com`. Если вы не знаете это значение, следуйте инструкциям документации SAP HANA Cloud Platform Identity Authentication по [настройке клиента SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).


5. В разделе **Настройка SAP HANA Cloud Platform Identity Authentication** щелкните **Настроить SAP HANA Cloud Platform Identity Authentication**, чтобы открыть диалоговое окно **Настройка единого входа**. Щелкните **SAML XML Metadata** (Метаданные XML-элемента SAML), а затем сохраните файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_08.png)


6. Чтобы настроить единый вход для приложения, перейдите к консоли администрирования SAP HANA Cloud Platform Identity Authentication. URL-адрес имеет следующий формат: `https://<tenant-id>.accounts.ondemand.com/admin`. Следуйте инструкциям документации SAP HANA Cloud Platform Identity Authentication, чтобы [настроить Microsoft Azure AD в качестве корпоративного поставщика удостоверений в SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html). 

7. На портале управления Azure нажмите кнопку **Сохранить**.
8. Продолжайте выполнять описанные действия, только если вы хотите добавить и включить единый вход для другого приложения SAP. Повторите шаги, описанные в разделе "Добавление приложения SAP HANA Cloud Platform Identity Authentication из коллекции", чтобы добавить другой экземпляр SAP HANA Cloud Platform Identity Authentication.
9. На портале управления Azure на странице интеграции приложения **SAP HANA Cloud Platform Identity Authentication** щелкните **Связанный единый вход**.

     ![Настройка связанного единого входа](./media/active-directory-saas-sap-hana-cloud-tutorial/linked_sign_on.png)
10. Сохраните конфигурацию.

>[!NOTE] 
>Новое приложение будет использовать конфигурацию единого входа для предыдущих приложений SAP. Убедитесь в том, что в консоли администрирования SAP HANA Cloud Platform Identity Authentication используются те же поставщики корпоративных удостоверений.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-hana-cloud-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>Создание тестового пользователя SAP HANA Cloud Platform Identity Authentication

В приложении SAP HANA Cloud Platform Identity Authentication не нужно создавать пользователя. Пользователи, находящиеся в хранилище пользователей в Azure AD, могут использовать функцию единого входа (SSO).

Приложение SAP HANA Cloud Platform Identity Authentication поддерживает параметр федерации удостоверений. Он позволяет приложению проверять, существуют ли пользователи, прошедшие проверку с помощью корпоративного поставщика удостоверений, в хранилище пользователей в приложении SAP HANA Cloud Platform Identity Authentication. По умолчанию параметр федерации удостоверений отключен. Если он включен, только пользователи, импортированные в SAP HANA Cloud Platform Identity Authentication, имеют доступ к приложению. 

Дополнительные сведения о включении или отключении параметра федерации удостоверений с помощью SAP HANA Cloud Platform Identity Authentication см. в разделе о включении этого параметра раздела [Configure Identity Federation with the User Store of SAP HANA Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Настройка параметра федерации удостоверений с использованием хранилища пользователей в приложении SAP HANA Cloud Platform Identity Authentication).


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP HANA Cloud Platform Identity Authentication.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SAP HANA Cloud Platform Identity Authentication, сделайте следующее:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP HANA Cloud Platform Identity Authentication**.

    ![Настройка единого входа](./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_sap_cloud_identity_09.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "SAP HANA Cloud Platform Identity Authentication" на панели доступа, вы автоматически войдете в приложение SAP HANA Cloud Platform Identity Authentication.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-tutorial/tutorial_general_203.png
