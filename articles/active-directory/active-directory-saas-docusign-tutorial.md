---
title: "Учебник. Интеграция Azure Active Directory с DocuSign | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и DocuSign."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6d2ab680afdd60732b8cf0a39a31ad6f35a70d09


---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Учебник. Интеграция Azure Active Directory с DocuSign
Цель данного руководства — показать интеграцию Azure и DocuSign.
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Тенант в DocuSign

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. [Включение интеграции приложений для DocuSign](#enabling-the-application-integration-for-docusign) 
2. [Настройка единого входа](#configuring-single-sign-on) 
3. [Настройка подготовки учетных записей](#configuring-account-provisioning) 
4. [Назначение пользователей](#assigning-users) 
   
![Настройка единого входа][0]

## <a name="enabling-the-application-integration-for-docusign"></a>Включение интеграции приложений для DocuSign
В этом разделе показано, как включить интеграцию приложений для DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для DocuSign, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Настройка единого входа][1]
2. Из списка Каталог выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Настройка единого входа][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне "Что необходимо сделать?" щелкните **Добавить приложение из коллекции**.
   
    ![Настройка единого входа][4]
6. В поле поиска введите **DocuSign**.
   
    ![Настройка единого входа][5]
7. В области результатов выберите **DocuSign** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Настройка единого входа][6]

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в DocuSign со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице **интеграции с приложением DocuSign** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно "Настройка единого входа".
   
    ![Настройка единого входа][7]
2. На странице **Как пользователи должны входить в DocuSign?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку "Далее".
   
    ![Настройка единого входа][8]
3. На странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа][61]
   
    а. В текстовом поле **URL-адрес для входа** введите `https://account.docusign.com/*`.  
   
    b. В текстовом поле **Идентификатор** введите `https://account.docusign.com/*`.  
   
    c. В нижней части страницы нажмите кнопку **Далее**. 

    > [!TIP] 
    > Значения URL-адреса и идентификатора представлены заполнителями. Инструкции по получению фактических значений для вашей среды приведены далее в этом разделе.


1. На странице **Настройка единого входа в DocuSign** щелкните **Скачать сертификат** и сохраните файл сертификата на локальном компьютере.
   
    ![Настройка единого входа][10]
2. В другом окне браузера войдите на **портал администрирования DocuSign** с правами администратора.
3. В меню навигации слева щелкните **Domains**(Домены).
   
    ![Настройка единого входа][51]
4. На правой панели щелкните **Claim Domain**(Зарезервировать домен).
   
    ![Настройка единого входа][52]
5. В диалоговом окне **Claim a domain** (Резервирование домена) в текстовом поле **Domain Name** (Доменное имя) введите соответствующее доменное имя своей компании и нажмите кнопку **Claim** (Зарезервировать). Обязательно проверьте домен. Состояние домена должно быть "Активный".
   
    ![Настройка единого входа][53]
6. На панели навигации слева щелкните **Identity Providers**  
   
    ![Настройка единого входа][54]
7. На панели справа нажмите кнопку **Add identity provider**(Добавить поставщик удостоверений). 
   
    ![Настройка единого входа][55]
8. На странице **параметров поставщика удостоверений** выполните следующие действия.
   
    ![Настройка единого входа][56]

    а. В соответствующее текстовое поле введите уникальное **имя** конфигурации. Не используйте пробелы.

    b. На классическом портале Azure скопируйте значение URL-адреса издателя и вставьте его в текстовое поле **Identity Provider Issuer** (Издатель поставщика удостоверений).

    В. На классическом портале Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений).

    d. На классическом портале Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Identity Provider Logout URL** (URL-адрес выхода поставщика удостоверений).

    д. Выберите **Sign AuthN Request**(Подпись запроса авторизации).

    Е. Для параметра **Send AuthN request by** (Как отправлять запрос авторизации) выберите значение **POST**.

    g. Для параметра **Send logout request by** (Как отправлять запрос на выход) выберите значение **POST**. 


1. В разделе **Custom Attribute Mapping** (Сопоставление настраиваемого атрибута) выберите поле, которое нужно сопоставить с утверждением Azure AD. Например, утверждение **emailaddress** сопоставляется со значением **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Это имя утверждения по умолчанию из Azure AD, которое используется для утверждения на электронную почту. 
   
    > [!NOTE]
    > Чтобы сопоставить пользователя Azure AD с пользователем Docusign, используйте соответствующий **идентификатор пользователя** . Выберите соответствующее поле и введите подходящее значение на основе параметров вашей организации.
    > 
    > 
   
    ![Настройка единого входа][57]
2. В разделе **Identity Provider Certificate** (Сертификат поставщика удостоверений) нажмите кнопку **Add Certificate** (Добавить сертификат) и передайте сертификат, который вы скачали на классическом портале Azure AD.   
   
    ![Настройка единого входа][58]
3. Щелкните **Сохранить**.
4. В разделе **Identity Providers** (Поставщики удостоверений) нажмите кнопку **Actions** (Действия), а затем выберите **Endpoints** (Конечные точки).   
   
    ![Настройка единого входа][59]
5. На классическом портале Azure перейдите на страницу **Настройка параметров приложения** . 
6. На **портале администрирования DocuSign** в разделе **View SAML 2.0 Endpoints** (Просмотр конечных точек SAML 2.0) выполните следующие действия:
   
    ![Настройка единого входа][60]
   
    а. Скопируйте значение поля **Service Provider Issuer URL** (URL-адрес издателя поставщика услуг) и вставьте его в текстовое поле **Идентификатор** на классическом портале Azure.
   
    b. Скопируйте значение поля **Service Provider Login URL** (URL-адрес входа поставщика услуг) и вставьте его в текстовое поле **URL-адрес для входа** на классическом портале Azure.
   
    c.  В нижней части страницы нажмите кнопку **Close**  
7. На классическом портале Azure щелкните **Далее**. 
8. На классическом портале Azure выберите **подтверждение конфигурации единого входа** и нажмите кнопку **Далее**.
   
    ![Приложения][14]
9. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![Приложения][15]

## <a name="configuring-account-provisioning"></a>Настройка подготовки учетных записей
В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для DocuSign.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. На странице **интеграции с приложением DocuSign** **классического портала Azure** щелкните **Настроить подготовку учетных записей**. Откроется диалоговое окно "Настроить подготовку учетных записей пользователей".
   
    ![Настройка подготовки учетных записей][30]
2. Чтобы включить автоматическую подготовку учетных записей пользователей, на странице **Параметры и учетные данные администратора** укажите данные учетной записи DocuSign с соответствующими правами и нажмите кнопку **Далее**. 
   
    ![Настройка подготовки учетных записей][31]
3. В диалоговом окне **Проверка подключения** нажмите кнопку **Начать тест**, а после успешного завершения проверки нажмите кнопку **Далее**.
   
    ![Настройка подготовки учетных записей][32]
4. Нажмите кнопку **Завершить** на странице **Подтверждение**.
   
    ![Настройка подготовки учетных записей][33]

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Чтобы назначить пользователей приложению DocuSign, сделайте следующее:
1. На **классическом портале Azure**создайте тестовую учетную запись.
2. На странице интеграции с приложением **DocuSign** нажмите кнопку **Назначить пользователей**.
   
    ![Назначение пользователей][40]
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Назначение пользователей][41]

Подождите 10 минут и убедитесь, что учетная запись синхронизирована с DocuSign.

В качестве первого шага проверки можно проверить состояние подготовки, щелкнув "Панель мониторинга" в разделе D на странице интеграции приложения DocuSign классического портала Azure.

![Назначение пользователей][42]

Об успешном завершении цикла подготовки пользователя говорит соответствующий статус:

![Назначение пользователей][43]

Если вы хотите проверить параметры единого входа, откройте панель доступа.

Дополнительные сведения о панели доступа см. в статье «Общие сведения о панели доступа».

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png



<!--HONumber=Nov16_HO3-->


