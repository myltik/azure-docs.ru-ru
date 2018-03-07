---
title: "Руководство по интеграции Azure Active Directory с Palo Alto Networks (пользовательский интерфейс администратора) | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Palo Alto Networks (пользовательский интерфейс администратора)."
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
ms.openlocfilehash: 60430f08f54232db619efd054ca3a7d9a44f4cdc
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Руководство по интеграции Azure Active Directory с Palo Alto Networks (пользовательский интерфейс администратора)

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с Palo Alto Networks (пользовательский интерфейс администратора).

Интеграция Azure AD с Palo Alto Networks (пользовательский интерфейс администратора) обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Palo Alto Networks (пользовательский интерфейс администратора).
- Вы можете включить автоматический вход пользователей в Palo Alto Networks (пользовательский интерфейс администратора) (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Palo Alto Networks (пользовательский интерфейс администратора), вам потребуется:

- подписка Azure AD;
- Брандмауэр следующего поколения Palo Alto Networks или Panorama Palo Alto Networks (централизованная система управления брандмауэрами)

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Palo Alto Networks (пользовательский интерфейс администратора) из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Добавление Palo Alto Networks (пользовательский интерфейс администратора) из коллекции.
Чтобы настроить интеграцию Palo Alto Networks (пользовательский интерфейс администратора) с Azure AD, необходимо добавить Palo Alto Networks (пользовательский интерфейс администратора) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Palo Alto Networks (пользовательский интерфейс администратора) из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Palo Alto Networks (пользовательский интерфейс администратора)** и выберите **Palo Alto Networks (пользовательский интерфейс администратора)** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Palo Alto Networks (пользовательский интерфейс администратора) в списке результатов](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Palo Alto Networks (пользовательский интерфейс администратора) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Palo Alto Networks (пользовательский интерфейс администратора) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Palo Alto Networks (пользовательский интерфейс администратора).

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Palo Alto Networks (пользовательский интерфейс администратора).

Чтобы настроить и проверить единый вход Azure AD в Palo Alto Networks (пользовательский интерфейс администратора), вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Palo Alto Networks (пользовательский интерфейс администратора)](#create-a-palo-alto-networks---admin-ui-test-user)** требуется для того, чтобы в Palo Alto Networks (пользовательский интерфейс администратора) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Palo Alto Networks (пользовательский интерфейс администратора).

**Чтобы настроить единый вход Azure AD в Palo Alto Networks (пользовательский интерфейс администратора), сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Palo Alto Networks (пользовательский интерфейс администратора)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Palo Alto Networks (пользовательский интерфейс администратора)** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Palo Alto Networks (пользовательский интерфейс администратора)](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Customer Firewall FQDN>/php/login.php`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<Customer Firewall FQDN>:443/SAML20/SP`
    
    c. В текстовое поле **URL-адрес ответа** введите URL-адрес службы обработчика утверждений (ACS) в следующем формате: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`.
    

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки Palo Alto Networks (пользовательский интерфейс администратора)](https://support.paloaltonetworks.com/support). 
 
4. Приложение Palo Alto Networks (пользовательский интерфейс администратора) ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на изображении выше, и выполните следующие шаги. (Обратите внимание, что значения атрибутов являются только примерами, укажите соответствующие значения для атрибутов username и adminrole.) Существует еще один необязательный атрибут accessdomain, который используется для ограничения доступа администратора к конкретным виртуальным системам в брандмауэре.
        
    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | Имя пользователя | user.userprincipalname |
    | adminrole | customadmin |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

    > [!NOTE]
    > Дополнительные сведения об атрибутах можно найти в следующих статьях.
    > 1. Профиль роли администратора для пользовательского интерфейса администратора (adminrole): https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile
    > 2. Домен доступа к устройству для пользовательского интерфейса администратора (accessdomain): https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain

6. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

8. В другом окне браузера откройте пользовательский интерфейс администратора брандмауэров Palo Alto с правами администратора.

9. Щелкните **Device** (Устройство).

    ![Настройка единого входа в Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

10. Чтобы импортировать файл метаданных, в левой области навигации выберите **SAML Identity Provider** (Поставщик удостоверений SAML) и нажмите кнопку Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

11. Сделайте следующее в окне Import (Импорт).

    ![Настройка единого входа в Palo Alto](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Укажите имя в текстовом поле **Имя профиля**, например "Пользовательский интерфейс администратора Azure AD".
    
    Б. В разделе **Identity Provider Metadata** (Метаданные поставщика удостоверений) щелкните **Browse** (Обзор) и выберите файл metadata.xml, загруженный ранее с портала Azure.
    
    c. Снимите флажок **Validate Identity Provider Certificate** (Проверить сертификат поставщика удостоверений).
    
    d. Щелкните **ОК**
    
    д. Зафиксируйте настройки брандмауэра, нажав кнопку **Зафиксировать**.

12. Выберите **SAML Identity Provider** (Поставщик удостоверений SAML) на панели навигации слева и щелкните профиль поставщика удостоверений SAML (например, "Пользовательский интерфейс администратора Azure AD"), созданный на предыдущем шаге. 
    
  ![Настройка единого входа в Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

13. Выполните следующие действия в окне **SAML Identity Provider Server Profile** (Профиль сервера поставщика удостоверений SAML):

  ![Настройка единого выхода из Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
  a. В текстовом поле **Identity Provider SLO URL** (URL-адрес SLO поставщика удостоверений) удалите ранее импортированный URL-адрес SLO и добавьте следующий URL-адрес: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
  
  Б. Щелкните **ОК**


14. В интерфейсе администратора брандмауэра Palo Alto Networks нажмите **Device** (Устройство) и выберите **Admin Roles** (Роли администратора)

15. Нажмите кнопку **Add** (Добавить). В окне Admin Role Profile (Профиль роли администратора) укажите имя для роли администратора (например, fwadmin). Имя роли администратора должно соответствовать имени атрибута SAML Admin Role (Роль администратора SAML), отправленному поставщиком удостоверений. На шаге 5 были созданы имя и значение роли администратора. 

  ![Настройка роли администратора Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
16. В пользовательском интерфейсе администратора брандмауэра последовательно выберите **Device** (Устройство) и **Authentication Profile** (Профиль аутентификации).

17. Нажмите кнопку **Add** (Добавить). В окне Authentication Profile (Профиль аутентификации) выполните следующие действия: 

 ![Настройка профиля аутентификации Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

   a. Укажите имя в текстовом поле **Name** (Имя), например AzureSAML_Admin_AuthProfile.
    
   Б. В раскрывающемся списке **Type** (Тип) выберите **SAML**. 
   
   c. В раскрывающемся списке IdP Server Profile (Профиль сервера поставщика удостоверений) выберите соответствующий профиль сервера поставщика удостоверений SAML (например, "Пользовательский интерфейс администратора Azure AD").
   
   c. Установите флажок **Enable Single Logout** (Включить единый выход).
    
   d. Введите имя атрибута (например, adminrole) в текстовое поле Admin Role Attribute (Атрибут роли администратора). 
   
   д. Выберите вкладку Advanced (Дополнительно) и нажмите кнопку **Add** (Добавить) в области Allow List (Список разрешений). Выберите всех или отдельных пользователей и группы, которые могут проходить аутентификацию с этим профилем. При аутентификации пользователя брандмауэр сопоставляет связанное имя пользователя или группу с записями в этом списке. Если не добавить записи, пользователи н е смогут пройти аутентификацию.
   
   ![Настройка профиля аутентификации Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
   
   f. Щелкните **ОК**

18. Чтобы включить использование администраторами единого входа SAML с помощью Azure, последовательно выберите **Device** (Устройство) и **Setup** (Настройка). В области Setup (Настройка) выберите вкладку **Management** (Управление) и щелкните значок шестеренки в разделе **Authentication Settings** (Параметры аутентификации). 

 ![Настройка параметров аутентификации Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

19. Выберите профиль аутентификации SAML, созданный на шаге 17 (например, AzureSAML_Admin_AuthProfile).

 ![Настройка параметров аутентификации Palo Alto Networks](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

20. Щелкните **ОК**

21. Зафиксируйте настройки, нажав кнопку **Commit** (Зафиксировать).


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Создание тестового пользователя в Palo Alto Networks (пользовательский интерфейс администратора)

Приложение Palo Alto Networks (пользовательский интерфейс администратора) поддерживает JIT-подготовку, поэтому после успешной проверки подлинности в системе будет автоматически создан пользователь, если он еще не существует. В этом разделе не нужно выполнять никаких действий.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Palo Alto Networks (пользовательский интерфейс администратора), чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Palo Alto Networks (пользовательский интерфейс администратора), выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Palo Alto Networks (пользовательский интерфейс администратора)**.

    ![Ссылка на Palo Alto Networks (пользовательский интерфейс администратора) в списке приложений](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Palo Alto Networks (пользовательский интерфейс администратора)" на панели доступа, вы автоматически войдете в приложение "Palo Alto Networks (пользовательский интерфейс администратора)".
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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

