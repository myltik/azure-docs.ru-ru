---
title: "Руководство по интеграции Azure Active Directory с Qlik Sense Enterprise | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Qlik Sense Enterprise."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 00dcedb09ea63b4337b2b730746fb2f5a1f27e64


---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Руководство. Интеграция Azure Active Directory с Qlik Sense Enterprise
В этом руководстве описано, как интегрировать Qlik Sense Enterprise с Azure Active Directory (Azure AD).

Интеграция Qlik Sense Enterprise с Azure AD обеспечивает следующие преимущества:

* С помощью Azure AD можно контролировать, кто будет иметь доступ к Qlik Sense Enterprise.
* Вы можете включить автоматический вход пользователей в Qlik Sense Enterprise (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Qlik Sense Enterprise, вам потребуется:

* подписка Azure AD;
* подписка Qlik Sense Enterprise с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Qlik Sense Enterprise из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Добавление Qlik Sense Enterprise из коллекции
Чтобы настроить интеграцию Qlik Sense Enterprise в Azure AD, необходимо добавить Qlik Sense Enterprise из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Qlik Sense Enterprise из коллекции, сделайте следующее:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **Qlik Sense Enterprise**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. В области результатов выберите **Qlik Sense Enterprise** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Qlik Sense Enterprise с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Qlik Sense Enterprise соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Qlik Sense Enterprise.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Qlik Sense Enterprise.

Чтобы настроить и проверить единый вход Azure AD в Qlik Sense Enterprise, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Qlik Sense Enterprise](#creating-a-qliksense-enterprise-test-user)** требуется для создания пользователя Britta Simon в Qlik Sense Enterprise, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В данном разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Qlik Sense Enterprise.

**Чтобы настроить единый вход Azure AD в Qlik Sense Enterprise, сделайте следующее:**

1. На странице интеграции с приложением **Qlik Sense Enterprise** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

2. На странице **Как пользователи должны входить в Qlik Sense Enterprise?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 
   
    А. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый для входа в приложение Qlik Sense Enterprise, в формате: **https://\<полное имя узла Qlik Sense\>:443/<префикс виртуального прокси-сервера\>/samlauthn/**.
   
    > [!NOTE]
    > Обратите внимание на косую черту в конце этого URI.  Она обязательная.
    > 
    > 
   
    b. click **Далее**

4. На странице **Настройка единого входа в Qlik Sense Enterprise** сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)
   
    а. Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.  Измените этот файл метаданных перед отправкой на сервер Qlik Sense.
   
    b. Нажмите кнопку **Далее**.

5. Подготовьте XML-файл метаданных федерации для отправки на сервер Qlik Sense.
   
    > [!NOTE]
    > Перед отправкой метаданных IdP на сервер Qlik Sense файл необходимо изменить. В нем нужно удалить определенные сведения, чтобы обеспечить корректное взаимодействие Azure AD и сервера Qlik Sense.
    > 
    > 
   
    ![QlikSense][qs24]
   
    а. Откройте файл FederationMetaData.xml, скачанный из Azure, в текстовом редакторе.
   
    b. Найдите значение **RoleDescriptor**.  Вы найдете четыре записи (две пары открывающих и закрывающих тегов элементов).
   
    c. Удалите теги RoleDescriptor и все данные между ними из файла.
   
    г) Сохраните файл и держите его под рукой для дальнейшего использования в этом документе.
6. Перейдите к консоли управления Qlik Management Console (QMC) Qlik Sense как пользователь с возможностью создания конфигураций виртуального прокси-сервера.
7. В QMC щелкните пункт меню Virtual Proxy (Виртуальный прокси-сервер).
   
    ![QlikSense][qs6] 
8. В нижней части страницы нажмите кнопку Create new (Создать).
   
    ![QlikSense][qs7]
9. Появится экран Virtual proxy edit (Редактирование виртуального прокси-сервера).  В правой части экрана расположено меню для отображения параметров конфигурации.
   
    ![QlikSense][qs9]
10. Установите галочку возле пункта меню Identification (Идентификация) и введите идентифицирующие сведения для конфигурации виртуального прокси-сервера Azure.
    
    ![QlikSense][qs8]  
    
    а. В поле Description (Описание) указывается понятное имя для конфигурации виртуального прокси-сервера.  Введите значение в поле Description (Описание).
    
    b. Значение в поле Prefix (Префикс) определяет конечную точку виртуального прокси-сервера для подключения к Qlik Sense с использованием единого входа Azure AD.  Введите уникальное имя префикса для этого виртуального прокси-сервера.
    
    c. Session inactivity timeout (minutes) (Время ожидания при бездействии сеанса (в минутах)) — время ожидания для подключений через этот виртуальный прокси-сервер.
    
    г) Session cookie header name (Имя заголовка файла cookie сеанса) — имя файла cookie, содержащего идентификатор для сеанса Qlik Sense, получаемое пользователем после успешной проверки подлинности.  Это имя должно быть уникальным.
11. Щелкните пункт меню Authentication (Проверка подлинности), чтобы сделать его видимым.  Появится экран Authentication (Проверка подлинности).
    
    ![QlikSense][qs10]
    
    а. Значение в раскрывающемся списке **Anonymous access mode** (Режим анонимного доступа) определяет, могут ли анонимные пользователи получить доступ к Qlik Sense через виртуальный прокси-сервер.  Параметр по умолчанию — No anonymous user (Без анонимных пользователей).
    
    b. В раскрывающемся меню **Authentication method** (Метод проверки подлинности) определяется схема проверки подлинности, используемая на виртуальном прокси-сервере.  Выберите пункт SAML в раскрывающемся списке.  В результате появятся дополнительные параметры.
    
    c. В поле **SAML host URI**(URI узла SAML) введите имя узла, которое пользователи будут вводить для получения доступа к Qlik Sense через этот виртуальный прокси-сервер SAML.  Имя узла представляет собой URI сервера Qlik Sense.
    
    d. В поле **SAML entity ID** (Идентификатор сущности SAML) введите то же значение, что и в поле SAML host URI (URI узла SAML).
    
    д. **SAML IdP metadata** (Метаданные IdP SAML) — это файл, измененный ранее в разделе **Изменение метаданных федерации из конфигурации Azure AD**.  **Перед отправкой метаданных IdP файл необходимо изменить.** В нем нужно удалить определенные сведения, чтобы обеспечить корректное взаимодействие между Azure AD и сервером Qlik Sense.  **Если файл нужно изменить, см. инструкции выше.**  Если файл изменен, щелкните кнопку Browse (Обзор) и выберите измененный файл метаданных для передачи в конфигурацию виртуального прокси-сервера.
    
    Е. Введите имя атрибута или ссылку на схему для атрибута SAML, представляющего **идентификатор пользователя** , который Azure AD отправит на сервер Qlik Sense.  Сведения о ссылке на схему доступны в постконфигурации экранов приложения Azure.  Чтобы использовать атрибут имени, введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    
    ж. Введите значение для **каталога пользователя** , который будет присоединен к пользователям при проверке подлинности на сервере Qlik Sense с использованием Azure AD.  Жестко заданные значения нужно заключить в **квадратные скобки []**.  Чтобы использовать атрибут, отправленный в утверждении SAML Azure AD, введите имя атрибута в этом текстовом поле **без** квадратных скобок.
    
    h. В раскрывающемся списке **SAML signing algorithm** (Алгоритм подписывания SAML) можно задать подписывание сертификата поставщика услуг (в этом случае сервер Qlik Sense) для конфигурации виртуального прокси-сервера.  Если сервер Qlik Sense использует доверенный сертификат, созданный с помощью Microsoft Enhanced RSA и AES Cryptographic Provider, измените алгоритм подписывания SAML на **SHA-256**.
    
    i. В разделе SAML attribute mapping (Сопоставление атрибутов SAML) можно отправлять дополнительные атрибуты, например группы, в Qlik Sense для использования в правилах безопасности.
12. Щелкните пункт меню Load balancing (Балансировка нагрузки), чтобы сделать его видимым.  Появится экран Load balancing (Балансировка нагрузки).
    
    ![QlikSense][qs11]
13. Нажмите кнопку Add new server node (Добавить новый узел сервера), выберите узел или узлы обработчика, на которые Qlik Sense будет отправлять сеансы для балансировки нагрузки, а затем нажмите кнопку Add (Добавить).
    
    ![QlikSense][qs12]
14. Щелкните пункт меню Advanced (Дополнительно), чтобы сделать его видимым. Откроется экран Advanced (Дополнительно).
    
    ![QlikSense][qs13]
    
    а. В разделе Host white list (Список разрешенных узлов) определяются имена принимаемых узлов при подключении к серверу Qlik Sense.  **Введите имя узла, которое пользователи будут указывать при подключении к серверу Qlik Sense.** Для имени узла указывается то же значение, что и для URI узла SAML, только без https://.
15. Нажмите кнопку Apply (Применить).
    
    ![QlikSense][qs14]
16. Нажмите кнопку OK (ОК), чтобы принять предупреждение о перезапуске прокси-серверов, связанных с виртуальным прокси-сервером.
    
    ![QlikSense][qs15]
17. В правой части экрана отображается меню Associated items (Связанные элементы).  Щелкните пункт меню Proxies (Прокси-серверы).
    
    ![QlikSense][qs16]
18. Появится экран прокси-сервера.  Нажмите кнопку Link (Связать) внизу, чтобы связать прокси-сервер с виртуальным прокси-сервером.
    
    ![QlikSense][qs17]
19. Выберите узел прокси-сервера для поддержки подключения к этому виртуальному прокси-серверу и нажмите кнопку Link (Связать).  После установки связи прокси-сервер отобразится в списке связанных прокси-серверов.
    
    ![QlikSense][qs18]
    ![QlikSense][qs19]
20. Через&5;–10 секунд появится сообщение об обновлении QMC.  Нажмите кнопку Refresh QMC (Обновить QMC).
    
    ![QlikSense][qs20]
21. После обновления QMC щелкните пункт меню Virtual proxies (Виртуальные прокси-серверы). Новая запись виртуального прокси-сервера SAML указана в таблице на экране.  Выделите запись виртуального прокси-сервера.
    
    ![QlikSense][qs51]
22. В нижней части экрана станет активной кнопка Download SP metadata (Скачать метаданные SP).  Нажмите ее, чтобы сохранить метаданные в файл.
    
    ![QlikSense][qs52]
23. Откройте файл метаданных SP.  Обратите внимание на записи **entityID** и **AssertionConsumerService**.  Значения этих записей должны быть эквивалентны **идентификатору** и **URL-адресу для входа** в конфигурации приложения Azure AD. Если они не совпадают, следует заменить их в мастере конфигурации приложения Azure AD.
    
    ![QlikSense][qs53]
24. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
    ![единого входа Azure AD][10]
25. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.  ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Создание тестового пользователя Qlik Sense Enterprise
В этом разделе описано, как создать пользователя Britta Simon в приложении Sense Enterprise. Чтобы добавить пользователя на платформе Qlik Sense Enterprise, обратитесь в службу поддержки Qlik Sense Enterprise.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Qlik Sense Enterprise.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в Qlik Sense Enterprise, сделайте следующее:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 

2. В списке приложений выберите **Qlik Sense Enterprise**.
   
    ![Настройка единого входа](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

## <a name="testing-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Qlik Sense Enterprise на панели доступа, вы автоматически войдете в приложение Qlik Sense Enterprise.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png



<!--HONumber=Feb17_HO3-->


