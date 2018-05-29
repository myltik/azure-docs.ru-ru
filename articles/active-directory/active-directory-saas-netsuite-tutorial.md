---
title: Руководство по интеграции Azure Active Directory с NetSuite | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: jeedes
ms.openlocfilehash: ecbadd45f964e4d4d36dcf67cf85cd878f012150
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140263"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Руководство по интеграции Azure Active Directory с NetSuite

В этом руководстве описано, как интегрировать Netsuite с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Netsuite обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Netsuite.
- Вы можете включить автоматический вход пользователей в Netsuite (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Netsuite, вам потребуется:

- подписка Azure AD;
- подписка Netsuite с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Workrite из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-netsuite-from-the-gallery"></a>Добавление Workrite из коллекции
Чтобы настроить интеграцию Netsuite с Azure AD, необходимо добавить Netsuite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Netsuite из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Netsuite**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. На панели результатов выберите **Netsuite** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Netsuite с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Netsuite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Netsuite.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Netsuite.

Чтобы настроить и проверить единый вход Azure AD в Netsuite, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Netsuite](#creating-a-netsuite-test-user)** требуется для того, чтобы в Netsuite существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Netsuite.

**Чтобы настроить единый вход Azure AD в Netsuite, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Netsuite** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Netsuite** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`.

    > [!NOTE] 
    > Значения, указанные выше, приведены в качестве примера. Измените эти значения, указав фактический URL-адрес ответа. Чтобы получить эти значения, обратитесь к [группе поддержки Netsuite](http://www.netsuite.com/portal/services/support.shtml).
 
4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Netsuite** щелкните **Настроить Netsuite**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Откройте новую вкладку в браузере и войдите как администратор корпоративного сайта NetSuite.

8. На панели инструментов в верхней части страницы щелкните **Setup** (Настройка) и выберите **Setup Manager** (Диспетчер настройки).

    ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Из списка **Setup Tasks** (Задачи настройки) выберите пункт **Integration** (Интеграция).

    ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. В разделе **Manage Authentication** (Управление аутентификацией) щелкните **SAML Single Sign-on** (Единый вход на основе SAML).

    ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. На странице **Настройка SAML** выполните следующие действия.
   
    a. Скопируйте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) из раздела **Краткий справочник** на странице **Настройка единого входа** и вставьте его в поле **Identity Provider Login Page** (Страница входа поставщика удостоверений) в Netsuite.

    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    Б. В NetSuite выберите раздел **Primary Authentication Method** (Основной метод проверки подлинности).

    c. В поле **SAMLV2 Identity Provider Metadata** (Метаданные поставщика удостоверений на основе SAMLv2) выберите **Upload IDP Metadata File** (Передать файл метаданных поставщика удостоверений). Затем нажмите кнопку **Обзор**, чтобы добавить файл метаданных, скачанный с портала Azure.

    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Нажмите кнопку **Submit**(Отправить).

12. В Azure AD установите флажок **Просмотреть и изменить все другие атрибуты пользователей** и добавьте атрибут.

    ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. В поле **Имя атрибута** введите `account`. В поле **Значение атрибута** введите идентификатор учетной записи NetSuite. Это значение является константой и изменяется вместе с учетной записью. Инструкции по поиску идентификатора учетной записи приведены ниже.

      ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. В верхнем меню навигации NetSuite щелкните **Setup** (Настройка).

    Б. Щелкните **Setup Tasks** (Задачи настройки) в меню навигации слева, выберите раздел **Integration** (Интеграция), а затем щелкните **Web Services Preferences** (Параметры веб-служб).

    c. Скопируйте идентификатор своей учетной записи NetSuite и вставьте его в поле **Значение атрибута** в Azure AD.

    ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Прежде чем пользователи смогут выполнять единый вход в NetSuite, необходимо назначить им соответствующие разрешения в NetSuite. Чтобы предоставить необходимые разрешения, выполните следующие инструкции.

    a. В верхнем меню навигации выберите **Setup** (Настройка) и щелкните **Setup Manager** (Диспетчер настройки).
      
      ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    Б. В меню навигации слева выберите **Users/Roles** (Пользователи и роли), а затем — **Manage Roles** (Управление ролями).
      
      ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Нажмите кнопку **Создать роль**.

    d. Введите **имя** для новой роли.
      
      ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    д. Выберите команду **Сохранить**.

    f. В верхнем меню щелкните **Разрешения**. Затем щелкните **Настройка**.
      
       ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    ж. Выберите **Set Up SAML Single Sign-on** (Настройка единого входа SAML) и нажмите кнопку **Добавить**.

    h. Выберите команду **Сохранить**.

    i. В верхнем меню навигации выберите **Setup** (Настройка) и щелкните **Setup Manager** (Диспетчер настройки).
      
       ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. В меню навигации слева выберите **Users/Roles** (Пользователи и роли), а затем — **Manage Users** (Управление пользователями).
      
       ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Выберите тестового пользователя. Нажмите кнопку **Изменить**.
      
       ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. В диалоговом окне "Роли" выберите роль, которую вы создали, и нажмите кнопку **Добавить**.
      
       ![Настройка единого входа](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Выберите команду **Сохранить**.
    
### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 

### <a name="creating-a-netsuite-test-user"></a>Создание тестового пользователя Netsuite

В этом разделе вы создадите в Netsuite пользователя с именем Britta Simon. Netsuite поддерживает JIT-подготовку. Эта функция включена по умолчанию.
В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в Netsuite, то он создается при попытке доступа к приложению Netsuite.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Netsuite.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Netsuite, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Netsuite**.

    ![Настройка единого входа](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Чтобы проверить параметры единого входа, откройте панель доступа по адресу [https://myapps.microsoft.com](https://myapps.microsoft.com/), выполните вход с тестовой учетной записью и щелкните **Netsuite**.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

