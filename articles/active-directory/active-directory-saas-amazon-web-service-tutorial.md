---
title: "Руководство по интеграции Azure Active Directory с Amazon Web Services (AWS) | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Amazon Web Services (AWS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Руководство по интеграции Azure Active Directory с Amazon Web Services

В этом руководстве описано, как интегрировать приложение Amazon Web Services (AWS) с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Amazon Web Services обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Amazon Web Services.
- Вы можете включить автоматический вход пользователей в Amazon Web Services (AWS) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Amazon Web Services, вам потребуется следующее:

- подписка Azure AD;
- подписка с поддержкой единого входа в Amazon Web Services.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [бесплатно получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Amazon Web Services из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Добавление Amazon Web Services (AWS) из коллекции
Чтобы настроить интеграцию Amazon Web Services с Azure AD, вам потребуется добавить Amazon Web Services из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Amazon Web Services (AWS) из коллекции, выполните следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Amazon Web Services (AWS)**. На панели результатов выберите **Amazon Web Services (AWS)** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Amazon Web Services (AWS) в списке результатов](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Amazon Web Services (AWS) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Amazon Web Services соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Amazon Web Services (AWS).

Чтобы установить связь, в Amazon Web Services (AWS) присвойте **имени пользователя** то же значение, которое присвоено **имени пользователя** в Azure AD. 

Чтобы настроить и проверить единый вход Azure AD в Amazon Web Services (AWS), вам потребуется выполнить действия в следующих стандартных блоках.

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Amazon Web Services](#create-an-amazon-web-services-aws-test-user) требуется для создания соответствующего пользователя Britta Simon в Amazon Web Services (AWS), связанного с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Amazon Web Services (AWS).

**Чтобы настроить единый вход Azure AD в Amazon Web Services (AWS), выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Amazon Web Services (AWS)** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить функцию единого входа, в диалоговом окне **Единый вход** из раскрывающегося списка **Режим** выберите пункт **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Amazon Web Services (AWS)** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Сведения о домене и URL-адресах для единого входа в приложение Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Приложение Amazon Web Services (AWS) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Значениями этих атрибутов можно управлять в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана показан пример.

    ![Настройка атрибута единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на предыдущем рисунке, а затем выполните шаги ниже.
    
    | Имя атрибута  | Значение атрибута | Пространство имен |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.Amazon.com/SAML/Attributes |
    | Роль            | user.assignedroles |  https://aws.Amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Настройте подготовку пользователя в Azure AD для получения всех ролей из консоли AWS. Выполните следующие шаги по подготовке.

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка атрибута единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Настройка атрибута единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. Из списка **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. В текстовом поле **Пространство имен** введите значение пространства имен, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

6. В разделе **Сертификат подписи SAML** выберите **XML метаданных**. Затем сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. В другом окне браузера войдите на сайт компании Amazon Web Services (AWS) как администратор.

9. Выберите **главную консоль**.
   
    ![Настройка главной страницы единого входа][11]

10. Выберите **Identity and Access Management** (Управление удостоверениями и доступом). 
   
    ![Настройка удостоверения для единого входа][12]

11. Выберите **Поставщики удостоверений**. Нажмите кнопку **Создать поставщик**. 
   
    ![Настройка поставщика единого входа][13]

12. В диалоговом окне **Configure Provider** (Настройка поставщика) выполните следующие действия. 
   
    ![Настройка диалогового окна единого входа][14]
 
    a. В качестве **типа поставщика** выберите **SAML**.

    Б. В текстовом поле **Provider Name** (Имя поставщика) введите имя поставщика (например, *WAAD*).

    c. Чтобы отправить загруженный **файл метаданных** с портала Azure, нажмите кнопку **Choose File** (Выбрать файл).

    d. Нажмите кнопку **Next Step** (Следующий шаг).

13. На странице диалогового окна **Verify Provider Information** (Проверка сведений о поставщике) нажмите кнопку **Создать**. 
    
    ![Настройка проверки единого входа][15]

14. Выберите **Роли**. Затем нажмите кнопку **Create New Role** (Создать роль). 
    
    ![Настройка ролей для единого входа][16]

15. В диалоговом окне **Set Role Name** (Настройка имени роли) выполните следующие действия. 
    
    ![Настройка имени для единого входа][17] 

    a. В текстовом поле **Role Name** (Имя роли) введите имя роли (например, *TestUser*). 

    Б. Нажмите кнопку **Next Step** (Следующий шаг).

16. В диалоговом окне **Select Role Type** (Выбор типа роли) выполните следующие действия: 
    
    ![Настройка типа роли для единого входа][18] 

    a. Выберите **Роль для доступа поставщика удостоверений**. 

    Б. В разделе **Grant Web Single Sign-On (WebSSO) access to SAML providers** (Предоставление единого входа через Интернет (WebSSO) для доступа к поставщикам SAML) щелкните **Select** (Выбрать).

17. В диалоговом окне **Establish Trust** (Установка доверия) выполните следующие действия:  
    
    ![Настройка доверия для единого входа][19] 

    a. Выберите поставщик SAML, созданный ранее (например, *WAAD*). 
  
    Б. Нажмите кнопку **Next Step** (Следующий шаг).

18. В диалоговом окне **Verify Role Trust** (Проверка доверия роли) нажмите кнопку **Next Step** (Следующий шаг). 
    
    ![Настройка доверия роли для единого входа][32]

19. В диалоговом окне **Attach Policy** (Присоединение политики) нажмите кнопку **Next Step** (Следующий шаг).  
    
    ![Настройка политики единого входа][33]

20. В диалоговом окне **Проверка** сделайте следующее:   
    
    ![Настройка проверки единого входа][34] 

    a. Нажмите кнопку **Create Role** (Создать роль).

    Б. Создайте необходимое количество ролей и сопоставьте их с поставщиком удостоверений.

21. Используйте учетные данные службы AWS для получения ролей из учетной записи AWS при подготовке пользователя Azure AD. Чтобы запустить эту задачу, откройте главную консоль Amazon Web Services (AWS).

22. Выберите **Services** (Службы) > **Security, Identity & Compliance (Безопасность, удостоверение и соответствие)** > **IAM**.

    ![Получение ролей из учетной записи Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. В разделе IAM выберите вкладку **Policies** (Политики).

    ![Получение ролей из учетной записи Amazon Web Services (AWS)](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Чтобы создать политику, нажмите кнопку **Create policy** (Создать политику).

    ![Создание политики](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. Чтобы создать собственную политику для получения всех ролей из учетных записей AWS, выполните следующие действия.

    ![Создание политики](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. В разделе **Create policy** (Создание политики) щелкните вкладку **JSON**.

    Б. Добавьте приведенный ниже код JSON в документ политики.
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Нажмите кнопку **Review Policy** (Просмотреть политику), чтобы проверить ее.

    ![Определение новой политики](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Определите **новую политику**, выполнив следующие действия.

    ![Определение новой политики](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. В поле **Policy Name** (Имя политики) введите **AzureAD_SSOUserRole_Policy**.

    Б. Вы можете указать следующее **описание** политики: **Эта политика позволит получить роли из учетных записей AWS**.
    
    c. Нажмите кнопку **Create Policy** (Создать политику).
        
27. Чтобы создать учетную запись пользователя в службе IAM для AWS, выполните следующие действия.

    a. Выберите **Users** (Пользователи) в консоли IAM Amazon Web Services (AWS).

    ![Определение новой политики](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    Б. Нажмите кнопку **Add user** (Добавить пользователя), чтобы создать пользователя.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. В разделе **Add user** (Добавление пользователя) выполните следующие действия.
    
    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * В поле имени пользователя введите **AzureADRoleManager**.
    
    * В поле для типа доступа выберите **Programmatic access** (Программный доступ). Таким образом пользователь сможет вызывать API-интерфейсы и получить роли из учетной записи AWS.
    
    * Нажмите кнопку **Next Permissions** (Следующие разрешения) в правом нижнем углу.

28. Создайте политику для этого пользователя, выполнив следующие действия.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Нажмите кнопку **Attach existing policies directly** (Подключить имеющиеся политики напрямую).

    Б. Выполните поиск только что созданной политики в разделе фильтра **AzureAD_SSOUserRole_Policy**.
    
    c. Выберите **политику**. Нажмите кнопку **Next: Review** (Далее: отзыв).

29. Просмотрите политику для подключенного пользователя, выполнив следующие действия.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Проверьте имя пользователя, тип доступа и политику, сопоставленную с пользователем.
    
    Б. Чтобы создать пользователя, нажмите кнопку **Create user** (Создать пользователя) в правом нижнем углу.

30. Скачайте учетные данные пользователя, выполнив действия ниже.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Скопируйте значения полей **Access key ID** (Идентификатор ключа доступа) и **Secret access key** (Секретный ключ доступа) для этого пользователя.
    
    Б. Введите эти учетные данные в раздел подготовки пользователя Azure AD, чтобы получить роли из консоли AWS.
    
    c. Нажмите кнопку **Закрыть** в правом нижнем углу.

31. Перейдите в раздел **User Provisioning** (Подготовка пользователя) приложения Amazon Web Services (AWS) на портале управления Azure AD.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Введите **ключ доступа** и **секрет** в поля **Секрет клиента** и **Секретный токен** соответственно.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Введите ключ доступа пользователя AWS в поле **ClientSecret**.
    
    Б. Введите секретный ключ пользователя AWS в поле **Секретный токен**.
    
    c. Нажмите кнопку **Проверить подключение**. Проверка этого подключения должна успешно завершиться.

    d. Сохраните введенный параметр, нажав кнопку **Сохранить** в верхней области.
 
33. В разделе **параметров** для поля "Состояние подготовки" выберите значение **Вкл**. После выбора значения **Вкл.** нажмите кнопку **Сохранить** в верхней области.

    ![Добавление пользователя](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** выберите вкладку **Единый вход**. Откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Чтобы отобразился список пользователей, выберите элемент **Пользователи и группы**. Затем выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно пользователя](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Создание тестового пользователя Amazon Web Services (AWS)

Цель этого раздела — создать пользователя с именем Britta Simon в Amazon Web Services. Для единого входа в системе служб Amazon Web Services (AWS) не требуется создавать пользователя, поэтому здесь не нужно выполнять никаких действий.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Amazon Web Services (AWS), чтобы этот пользователь мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Amazon Web Services (AWS), сделайте следующее:**

1. На портале Azure откройте представление "Приложения". В представлении каталога выберите пункт **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Amazon Web Services (AWS)**.

    ![Ссылка на Amazon Web Services (AWS) в списке приложений](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** из списка "Пользователи" выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**. 

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Amazon Web Services (AWS) на панели доступа, вы автоматически войдете в приложение Amazon Web Services (AWS). Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

