---
title: Руководство по интеграции Azure Active Directory с Palo Alto Networks (пользовательский интерфейс администратора) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks (пользовательский интерфейс администратора).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: aa3366810a40b004fe510cb2909f8da0f3513ddb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Руководство по интеграции Azure Active Directory с Palo Alto Networks (пользовательский интерфейс администратора)

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с Palo Alto Networks (пользовательский интерфейс администратора).

Интеграция Azure AD с Palo Alto Networks (пользовательский интерфейс администратора) обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Palo Alto Networks (пользовательский интерфейс администратора).
- Вы можете включить автоматический (единый) вход пользователей в Palo Alto Networks (пользовательский интерфейс администратора) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно, через портал Azure.

Чтобы узнать об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Palo Alto Networks (пользовательский интерфейс администратора), вам потребуется:

- подписка Azure AD;
- Брандмауэр следующего поколения Palo Alto Networks или Panorama Palo Alto Networks (централизованная система управления брандмауэрами)

> [!NOTE]
> Мы *не рекомендуем* использовать рабочую среду для тестирования действий, описанных в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом руководстве, состоит из двух основных блоков:

* Добавление Palo Alto Networks (пользовательский интерфейс администратора) из коллекции.
* настройка и проверка единого входа в Azure AD.

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Добавление Palo Alto Networks (пользовательский интерфейс администратора) из коллекции
Чтобы настроить интеграцию Azure AD с Palo Alto Networks (пользовательский интерфейс администратора), необходимо добавить интерфейс из коллекции в список управляемых приложений SaaS:

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Окно "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Palo Alto Networks (пользовательский интерфейс администратора)** и выберите **Palo Alto Networks (пользовательский интерфейс администратора)** на панели результатов, а затем щелкните **Добавить**, чтобы выполнить соответствующее действие.

    ![Palo Alto Networks (пользовательский интерфейс администратора) в списке результатов](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Palo Alto Networks (пользовательский интерфейс администратора) с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход, в Azure AD необходимо определить, какой пользователь в Palo Alto Networks (пользовательский интерфейс администратора) соответствует пользователю в Azure AD. Другими словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем Palo Alto Networks (пользовательский интерфейс администратора).

Чтобы установить эту связь, назначьте *имя пользователя* в Azure AD в качестве *имени пользователя* в Palo Alto Networks (пользовательский интерфейс администратора).

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks (пользовательский интерфейс администратора), выполните действия в следующих пяти разделах.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Включите единый вход Azure AD на портале Azure и настройте его в приложении Palo Alto Networks (пользовательский интерфейс администратора):

1. На портале Azure на странице интеграции с приложением **Palo Alto Networks (пользовательский интерфейс администратора)** выберите пункт **Единый вход**.

    ![Ссылка "Единый вход"][4]

2. В окне **Единый вход** в поле **Режим единого входа** выберите **Вход на основе SAML**.
 
    ![Окно "Единый вход"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Palo Alto Networks (пользовательский интерфейс администратора)**, сделайте следующее:

    ![Сведения о едином входе в разделе "Домены и URL-адреса приложения Palo Alto Networks (пользовательский интерфейс администратора)"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. В поле **URL-адрес входа** введите URL-адрес в следующем формате: *https://\<FQDN брандмауэра клиента>/php/login.php*.

    Б. В поле **Идентификатор** введите URL-адрес в следующем формате: *https://\<FQDN брандмауэра клиента>:443/SAML20/SP*.
    
    c. В поле **URL-адрес ответа** введите URL-адрес службы обработчика утверждений (ACS) в следующем формате: *https://\<FQDN брандмауэра клиента>:443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Приведенные выше значения используются только для примера. Замените их фактическими значениями URL-адреса входа и идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки Palo Alto Networks (пользовательский интерфейс администратора)](https://support.paloaltonetworks.com/support). 
 
4. Так как в приложении Palo Alto Networks (пользовательский интерфейс администратора) предусмотрен определенный формат утверждений SAML, настройте утверждения, как показано на рисунке ниже. Измените значения атрибутов в разделе **Атрибуты пользователя** на странице **интеграции приложения**, выполнив шаги ниже:
    
    ![Список атрибутов токена SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Значения атрибутов указаны только для примера, поэтому задайте соответствующие значения для *username* и *adminrole*. *Accessdomain* — необязательный атрибут, который используется для ограничения доступа администратора к конкретным виртуальным системам в брандмауэре.
   >
        
    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | Имя пользователя | user.userprincipalname |
    | adminrole | customadmin |

    a. Нажмите кнопку **Добавить атрибут**.  
    
    ![Кнопка "Добавить атрибут"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    Откроется окно **Добавление атрибута**.

    ![Окно "Добавление атрибута"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В поле **Значение** введите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

    > [!NOTE]
    > Дополнительные сведения об этих атрибутах см. в следующих статьях:
    > * [Administrative role profile for Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile) (Профиль роли администратора для пользовательского интерфейса администратора (adminrole))
    > * [Device access domain for Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain) (Домен доступа к устройствам для пользовательского интерфейса администратора (accessdomain))
    >

5. В разделе **Сертификат подписи SAML** щелкните ссылку **XML метаданных**, а затем нажмите кнопку **Сохранить**.

    ![Ссылка для скачивания XML-файла метаданных](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![Кнопка "Сохранить"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

6. В новом окне браузера откройте пользовательский интерфейс администратора брандмауэра Palo Alto с правами администратора.

7. Выберите вкладку **Device** (Устройство).

    ![Вкладка Device (Устройство)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

8. В области слева выберите пункт **SAML Identity Provider** (Поставщик удостоверений SAML) и нажмите кнопку **Import** (Импорт).

    ![Кнопка импорта файла метаданных](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

9. В окне **SAML Identify Provider Server Profile Import** (Импорт профиля сервера поставщика удостоверений SAML):

    ![Окно SAML Identify Provider Server Profile Import (Импорт профиля сервера поставщика удостоверений SAML)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Укажите имя в текстовом поле **Profile Name** (Имя профиля), например **Azure AD Admin UI** (Пользовательский интерфейс администратора Azure AD).
    
    Б. В разделе **New Identity Provider** (Новый поставщик удостоверений) щелкните **Обзор** и выберите XML-файл метаданных, скачанный на портале Azure.
    
    c. Снимите флажок **Validate Identity Provider Certificate** (Проверить сертификат поставщика удостоверений).
    
    d. Нажмите кнопку **ОК**.
    
    д. Примените настройки брандмауэра, нажав кнопку **Commit** (Применить).

10. В области слева выберите пункт **SAML Identity Provider** (Поставщик удостоверений SAML), а затем профиль поставщика удостоверений (например, **AzureAD Admin UI** (Пользовательский интерфейс администратора Azure AD)), созданный на предыдущем шаге. 

    ![Профиль поставщика удостоверений SAML](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

11. В окне **SAML Identify Provider Server Profile** (Профиль сервера поставщика удостоверений SAML) сделайте следующее:

    ![Окно SAML Identify Provider Server Profile (Профиль сервера поставщика удостоверений SAML)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. В поле **Identity Provider SLO URL** (URL-адрес выхода для поставщика удостоверений) замените экспортированный адрес следующим: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    Б. Нажмите кнопку **ОК**.

12. В интерфейсе администратора брандмауэра Palo Alto Networks щелкните **Device** (Устройство) и **Admin Roles** (Роли администратора).

13. Нажмите кнопку **Добавить**. 

14. В окне **Admin Role Profile** (Профиль роли администратора) в поле **Name** (Имя) укажите имя для роли администратора (например, **fwadmin**).  
    Имя администраторской роли должно соответствовать имени атрибута SAML Admin Role (Роль администратора SAML), отправленному поставщиком удостоверений. Имя и значение роли администратора заданы на шаге 4.

    ![Настройка роли администратора Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
15. В пользовательском интерфейсе администратора брандмауэра последовательно выберите **Device** (Устройство) и **Authentication Profile** (Профиль аутентификации).

16. Нажмите кнопку **Добавить**. 

17. В окне **Authentication Profile** (Профиль аутентификации) выполните следующие действия: 

    ![Окно "Authentication Profile" (Профиль аутентификации)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Укажите имя в текстовом поле **Name** (Имя), например **AzureSAML_Admin_AuthProfile**.
    
    Б. В раскрывающемся списке **Type** (Тип) выберите пункт **SAML**. 
   
    c. В раскрывающемся списке **IdP Server Profile** (Профиль сервера поставщика удостоверений) выберите соответствующий профиль сервера поставщика удостоверений SAML (к примеру, **AzureAD Admin UI** (Пользовательский интерфейс администратора Azure AD)).
   
    c. Установите флажок **Enable Single Logout** (Включить единый выход).
    
    d. В поле **Admin Role Attribute** ("Атрибут роли администратора") введите имя атрибута (например, **adminrole**). 
    
    д. Выберите вкладку **Advanced** (Расширенный), а затем в разделе **Allow List** (Список разрешений) нажмите кнопку **Add** (Добавить). 
    
    ![Кнопка Add (Добавить) на вкладке Advanced (Расширенный)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Установите флажок **All** (Все) или выберите пользователей или группы, которые смогут проходить аутентификацию с этим профилем.  
    При аутентификации пользователя брандмауэр сопоставляет связанное имя пользователя или группу с записями в этом списке. Если не добавить записи, пользователи н е смогут пройти аутентификацию.

    ж. Нажмите кнопку **ОК**.

18. Чтобы администраторы могли использовать единый вход SAML с Azure, выберите **Device (Устройство)** > **Setup (Установка)**. На панели **Setup** (Установка) выберите вкладку **Management** (Управление), а затем в разделе **Authentication Settings (Параметры проверки подлинности)** нажмите кнопку **Settings** (в виде шестеренки). 

 ![Кнопка Settings (Параметры)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Выберите профиль проверки подлинности SAML, созданный на шаге 17 (например, **AzureSAML_Admin_AuthProfile**).

 ![Окно "Authentication Profile" (Профиль аутентификации)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Нажмите кнопку **ОК**.

21. Чтобы сохранить параметры, щелкните **Commit** ("Применить").


> [!TIP]
> Настроив приложение, вы можете прочитать краткую версию предыдущих инструкций на [портале Azure](https://portal.azure.com). Добавив приложение из раздела **Active Directory** > **Корпоративные приложения**, выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** внизу. На [этой странице]( https://go.microsoft.com/fwlink/?linkid=845985) вы можете получить дополнительную информацию о встроенной документации Azure AD.
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе вы создадите на портале Azure тестового пользователя Britta Simon, выполнив следующие действия.

![Создание тестового пользователя Azure AD][100]

1. На портале Azure в области слева щелкните **Azure Active Directory**.

    ![Ссылка на Azure Active Directory](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Чтобы отобразить список активных пользователей, выберите **Пользователи и группы** > **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. В верхней части окна **Все пользователи** выберите **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Откроется окно **Пользователь**.

4. В окне **Пользователь** сделайте следующее.

    ![Окно пользователя](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Создание тестового пользователя в Palo Alto Networks (пользовательский интерфейс администратора)

Palo Alto Networks (пользовательский интерфейс администратора) поддерживает подготовку пользователей JIT. Если пользователя еще не создали, он будет создан в системе автоматически после успешной проверки подлинности. Дополнительные действия по созданию пользователя не требуются.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Palo Alto Networks (пользовательский интерфейс администратора), чтобы он мог использовать единый вход Azure. Для этого выполните следующее.

![Назначение роли пользователя][200] 

1. На портале Azure откройте представление **Приложения**, перейдите к представлению **Каталог** и выберите **Корпоративные приложения** > **Все приложения**.

    ![Ссылки "Корпоративные приложения" и "Все приложения"][201] 

2. В списке **Приложения** выберите пункт **Palo Alto Networks (пользовательский интерфейс администратора)**.

    ![Ссылка на Palo Alto Networks (пользовательский интерфейс администратора)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. В области слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**, затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. Нажмите кнопку **Select** (Выбрать).

7. В окне **Добавление назначения** выберите **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Palo Alto Networks (пользовательский интерфейс администратора)" на панели доступа, вы автоматически войдете в приложение Palo Alto Networks (пользовательский интерфейс администратора).

Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список руководств, посвященных интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

