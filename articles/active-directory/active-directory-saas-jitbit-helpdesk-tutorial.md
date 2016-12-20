---
title: "Учебник. Интеграция Azure Active Directory с Jitbit Helpdesk | Документация Майкрософт"
description: "Узнайте, как использовать Jitbit Helpdesk вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: acffb1e0b3f1ac05034d130ca3a24246154a0cc2


---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Руководство. Интеграция Azure Active Directory с Jitbit Helpdesk
Цель данного руководства — показать интеграцию Azure и Jitbit Helpdesk.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Jitbit Helpdesk

По завершении работы с этим руководством пользователи Azure AD, назначенные в Jitbit Helpdesk, смогут выполнять единый вход в приложение на веб-сайте Jitbit Helpdesk компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Jitbit Helpdesk
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenario")

## <a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Включение интеграции приложений для Jitbit Helpdesk
В этом разделе показано, как включить интеграцию приложений для Jitbit Helpdesk.

### <a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Jitbit Helpdesk, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Jitbit Helpdesk**.
   
   ![Коллекция приложений](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Application Gallery")
7. В области результатов выберите **Jitbit Helpdesk** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в Jitbit Helpdesk со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. .  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

> [!IMPORTANT]
> Чтобы иметь возможность настроить единый вход для клиента Jitbit Helpdesk, необходимо сначала обратиться в службу технической поддержки Jitbit Helpdesk для включения соответствующей функции.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Jitbit Helpdesk** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в Jitbit Helpdesk?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Jitbit Helpdesk** введите свой URL-адрес в формате *https://\<имя_клиента\>.Jitbit.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configure app URL")
4. На странице **Настройка единого входа в Jitbit Helpdesk** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата локально в формате **c:\\Jitbit Helpdesk.cer**.
   
   ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configure single sign-on")
5. В другом окне браузера войдите на свой корпоративный веб-сайт Jitbit Helpdesk в качестве администратора.
6. На панели инструментов в верхней части страницы щелкните **Администрирование**.
   
   ![Администрирование](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
7. Щелкните **Общие параметры**.
   
   ![Пользователи, компании и разрешения](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")
8. В разделе конфигурации **Параметры проверки подлинности** сделайте следующее:
   
   ![Параметры проверки подлинности](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Authentication settings")
   
   1. Установите флажок **Enable SAML 2.0 single sign on** (Включить единый вход SAML 2.0) для выполнения единого входа с помощью **OneLogin**.
   2. На странице диалогового окна **Настройка единого входа в Jitbit Helpdesk** классического портала Azure скопируйте значение поля **Конечная точка, инициированная поставщиком услуг** и вставьте его в текстовое поле **EndPoint URL** (URL-адрес конечной точки).
   3. Создайте файл **в кодировке Base-64** из скачанного сертификата.
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Откройте сертификат в кодировке Base-64, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509** .
   5. Нажмите кнопку **Сохранить изменения**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Jitbit Helpdesk, они должны быть подготовлены для Jitbit Helpdesk.  
В случае с Jitbit Helpdesk подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход в клиент **Jitbit Helpdesk** .
2. В меню в верхней части страницы щелкните **Администрирование**.
   
   ![Администрирование](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
3. Нажмите кнопку **Пользователи, компании и разрешения**.
   
   ![Пользователи, компании и разрешения](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")
4. Нажмите кнопку **Добавить пользователя**.
   
   ![Добавить пользователя](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Add user")
5. В разделе "Create" (Создание) введите данные учетной записи Azure AD, которую необходимо подготовить, в следующие текстовые поля: **Username** (Имя пользователя), **Email** (Адрес электронной почты), **First Name** (Имя), **Last Name** (Фамилия).
   
   ![Создание](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Create")
6. Щелкните **Создать**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Jitbit Helpdesk или API, предоставляемые Jitbit Helpdesk для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Чтобы назначить пользователей Jitbit Helpdesk, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Jitbit Helpdesk** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


