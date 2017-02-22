---
title: "Руководство по интеграции Azure Active Directory с ShiftPlanning | Документация Майкрософт"
description: "Узнайте, как использовать ShiftPlanning с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 6104bd7e22d855c4c8737ef1080bfd445b29eafb


---
# <a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>Учебник. Интеграция Azure Active Directory с ShiftPlanning
Цель данного учебника — показать интеграцию Azure и ShiftPlanning.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* подписка ShiftPlanning с поддержкой единого входа.

По завершении работы с этим руководством пользователи Azure AD, назначенные в ShiftPlanning, смогут выполнять единый вход в приложение на веб-сайте ShiftPlanning компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для ShiftPlanning
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Сценарий")

## <a name="enabling-the-application-integration-for-shiftplanning"></a>Включение интеграции приложений для ShiftPlanning
В этом разделе показано, как включить интеграцию приложений для ShiftPlanning.

### <a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для ShiftPlanning, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Приложения")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавление приложения](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Добавление приложения")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавление приложения из коллекции](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Добавление приложения из коллекции")

6. В **поле поиска** введите **ShiftPlanning**.
   
    ![Коллекция приложений](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Коллекция приложений")

7. В области результатов выберите **ShiftPlanning** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в ShiftPlanning со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **ShiftPlanning** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Configure Single Sign On** (Настройка единого входа).
   
    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Настройка единого входа")

2. На странице **Как пользователи должны входить в ShiftPlanning?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Настройка единого входа")

3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в ShiftPlanning** введите свой URL-адрес, используя формат *https://company.shiftplanning.com/includes/saml/*, а затем нажмите кнопку **Далее**.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Настройка URL-адреса приложения")

4. Чтобы скачать сертификат, на странице **Настройка единого входа в ShiftPlanning** щелкните **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Настройка единого входа")

5. В другом окне веб-браузера войдите на веб-сайт **ShiftPlanning** компании в качестве администратора.
6. В верхнем меню щелкните **Администратор**.
   
    ![Администратор](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Администратор")

7. В разделе **Integration** (Интеграция) щелкните **Single Sign-On** (Единый вход).
   
    ![Единый вход](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Единый вход")

8. В разделе **Единый вход** сделайте следующее:
   
    ![Единый вход](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Единый вход")
   
    1. Установите флажок **SAML включен**.

    2. Установите флажок **Разрешить вход с паролем**

    3. На странице диалогового окна **Настройка единого входа в ShiftPlanning** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **SAML Issuer URL** (URL-адрес издателя SAML).

    4. На странице диалогового окна **Настройка единого входа в ShiftPlanning** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес удаленного выхода**.

    5. Создайте файл в кодировке **Base-64** из загруженного сертификата.  
       
    > [!TIP]
    > Дополнительные сведения можно узнать из видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 

    6. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509** .

    7. Нажмите кнопку **Сохранить параметры**.

9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Настройка единого входа")
   
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в ShiftPlanning, они должны быть подготовлены в ShiftPlanning.  
В случае ShiftPlanning подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Войдите на веб-сайт **ShiftPlanning** компании в качестве администратора.

2. Щелкните **Администратор**.
   
    ![Администратор](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Администратор")

3. Щелкните **Персонал**.
   
    ![Персонал](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Персонал")

4. В разделе **Actions** (Действия) щелкните **Add Employee** (Добавить сотрудника).
   
    ![Добавить сотрудников](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Добавить сотрудников")

5. В разделе **Добавление сотрудников** сделайте следующее:
   
    ![Сохранить сотрудников](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Сохранить сотрудников")
   
    1. Заполните текстовые поля **First Name** (Имя), **Last Name** (Фамилия) и **Email** (Электронный адрес) данными действующей учетной записи AAD, которую необходимо подготовить.

    2. Щелкните **Сохранить сотрудников**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователей ShiftPlanning или API, предоставляемые ShiftPlanning для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>Чтобы назначить пользователей ShiftPlanning, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением **ShiftPlanning** щелкните **Назначить пользователей**.
   
    ![Назначение пользователей](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Назначение пользователей")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Да")
 
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


