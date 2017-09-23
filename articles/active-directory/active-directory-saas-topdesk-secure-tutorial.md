---
title: "Руководство по интеграции Azure Active Directory с TOPdesk — Secure | Документация Майкрософт"
description: "Узнайте, как использовать TOPdesk — Secure с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и многого другого."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 28f0542dbe87bb34c83a7852db7c3a9fef055ce9
ms.contentlocale: ru-ru
ms.lasthandoff: 02/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Учебник. Интеграция Azure Active Directory с TOPdesk — Secure
Цель данного учебника — показать интеграцию Azure и TOPdesk — Secure.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка TOPdesk — Secure с поддержкой единого входа.

По завершении работы с этим руководством пользователи Azure AD, назначенные в TOPdesk — Secure, смогут выполнять единый вход в приложение на веб-сайте TOPdesk — Secure вашей компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для TOPdesk — Secure
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Сценарий")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>Включение интеграции приложений для TOPdesk — Secure
В этом разделе показано, как включить интеграцию приложений для TOPdesk — Secure.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для TOPdesk — Secure, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Приложения")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавление приложения](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Добавление приложения")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавление приложения из коллекции](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Добавление приложения из коллекции")

6. В **поле поиска** введите **TOPdesk — Secure**.
   
    ![Коллекция приложений](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Коллекция приложений")

7. В области результатов выберите **TOPdesk — Secure** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![TOPdesk — Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk — Secure")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в TOPdesk — Secure с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  
Для настройки единого входа для TOPdesk — Secure вам необходимо отправить файл значка с логотипом. Для получения файла значка обратитесь в службу поддержки TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. Выполните вход на веб-сайт **TOPdesk — Secure** своей компании в качестве администратора.
2. В меню **TOPdesk** выберите пункт **Settings** (Параметры).
   
    ![Параметры](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Параметры")

3. Щелкните **Параметры входа**.
   
    ![Параметры входа](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Параметры входа")

4. Разверните меню **Login Settings** (Параметры входа), а затем выберите **General** (Общие).
   
    ![Общие](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Общие")

5. В разделе **Secure** (Безопасность) для конфигурации **SAML login** (Вход SAML) сделайте следующее.
   
    ![Технические параметры](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Технические параметры")
   
    а. Нажмите кнопку **Скачать** , чтобы скачать общедоступный файл метаданных, а затем сохраните его локально на компьютере.
   
    b. Откройте файл метаданных и определите местоположение узла **AssertionConsumerService** .
    
    ![Служба обработчика утверждений](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Служба обработчика утверждений")
   
    c. Скопируйте значение **AssertionConsumerService** .  
      
    > [!NOTE]
    > Это значение потребуется в разделе **Настройка URL-адреса приложения** далее в этом руководстве.
    > 
    > 

6. В другом окне веб-браузера войдите на **классический портал Azure** в качестве администратора.

7. На странице интеграции с приложением **TOPdesk — Secure** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Настройка единого входа")

8. На странице **Как пользователи будут входить в TOPdesk — Secure** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Настройка единого входа")

9. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Настройка URL-адреса приложения")
   
    а. В текстовом поле **URL-адрес для входа в TOPdesk — Secure** введите URL-адрес, используемый пользователями для входа в ваше приложение TOPdesk — Secure (например, *https://qssolutions.topdesk.net*).
   
    b. В текстовое поле **URL-адрес ответа TOPdesk — Secure** вставьте значение **TOPdesk - Secure AssertionConsumerService URL** (URL-адрес AssertionConsumerService для TOPdesk — Secure) (например, *https://qssolutions.topdesk.net/tas/public/login/saml*).
   
    c. Нажмите кнопку **Далее**.

10. На странице **Настройка единого входа в TOPdesk — Secure** щелкните **Скачать метаданные**, чтобы скачать метаданные, а затем сохраните файл данных локально на компьютере.
    
    ![Настройка единого входа](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Настройка единого входа")

11. Чтобы создать файл сертификата, выполните следующие действия:
    
    ![Сертификат](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Сертификат")
    
    а. Откройте скачанный файл метаданных.
    b. Разверните узел **RoleDescriptor**, содержащий **xsi:type** со значением **fed:ApplicationServiceType**.
    c. Скопируйте значение узла **X509Certificate** .
    d. Сохраните скопированное значение узла **X509Certificate** локально в файл на компьютере.

12. На веб-сайте TOPdesk — Secure своей компании в меню **TOPdesk** выберите **Settings** (Параметры).
    
    ![Параметры](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Параметры")

13. Щелкните **Параметры входа**.
    
    ![Параметры входа](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Параметры входа")

14. Разверните меню **Login Settings** (Параметры входа), а затем выберите **General** (Общие).
    
    ![Общие](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Общие")

15. В разделе **Public** (Общедоступные) щелкните **Add** (Добавить).
    
    ![Добавление](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Добавление")

16. На странице диалогового окна **Помощник настройки SAML** сделайте следующее:
    
    ![Помощник по настройке SAML](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Помощник по настройке SAML")
    
    а. Чтобы отправить скачанный файл метаданных, напротив пункта **Federation Metadata** (Метаданные федерации) нажмите кнопку **Browse** (Обзор).

    b. Чтобы отправить файл сертификата, напротив пункта **Certificate (RSA)** (Сертификат (RSA)) нажмите кнопку **Browse** (Обзор).

    c. Чтобы отправить файл с логотипом, полученный от службы поддержки TOPdesk, напротив пункта **Logo icon** (Значок логотипа) нажмите кнопку **Browse** (Обзор).

    d. В текстовом поле **User name attribute** (Атрибут имени пользователя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    д. В текстовом поле **Отображаемое имя** введите имя конфигурации.

    Е. Щелкните **Сохранить**.

17. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Настройка единого входа")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в систему TOPdesk — Secure, они должны быть подготовлены для нее.  
В случае TOPdesk — Secure подготовка пользователей осуществляется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт **TOPdesk — Secure** компании в качестве администратора.
2. В меню в верхней части страницы щелкните **TOPdesk \> New (Создать) \> Support Files (Файлы поддержки) \> Operator (Оператор)**.
   
    ![Оператор](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Оператор")

3. В диалоговом окне **Новый оператор** выполните следующие действия:
   
    ![Новый оператор](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Новый оператор")
   
    а. Перейдите на вкладку "Общее".
   
    b. В текстовом поле **Surname** (Фамилия) в разделе **General** (Общее) введите фамилию пользователя действующей учетной записи Azure Active Directory, которую нужно подготовить.
   
    c. Выберите для этой учетной записи **Site** (Веб-сайт) в разделе **Location** (Расположение).
   
    d. В текстовом поле **Login Name** (Имя для входа в систему) в разделе **TOPdesk Login** (Вход в TOPdesk) введите имя для входа пользователя.
   
    д. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователей TOPdesk — Secure или API-интерфейсы, предоставляемые TOPdesk — Secure для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Чтобы назначить пользователей TOPdesk — Secure, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **TOPdesk — Secure** нажмите кнопку **Назначить пользователей**.
   
    ![Назначение пользователей](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Назначение пользователей")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


