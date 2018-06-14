---
title: Руководство по интеграции Azure Active Directory с SAP Business Object Cloud | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 5a56a892ac3b28c4e90ec2ea6360da3d2eff2581
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349494"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Руководство по интеграции Azure Active Directory с SAP Business Object Cloud

В этом руководстве описано, как интегрировать SAP Business Object Cloud с Azure Active Directory (Azure AD).

Интеграция SAP Business Object Cloud с Azure AD обеспечивает следующие преимущества:

- С помощью Microsoft Azure AD вы можете контролировать доступ к приложению SAP Business Object Cloud.
- Вы можете включить автоматический вход пользователей в SAP Business Object Cloud с использованием функции единого входа и учетной записи пользователя в Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SAP Business Object Cloud, вам потребуется:

- подписка Azure AD;
- подписка SAP Business Object Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий, выполняемых в этом руководстве.

Для проверки действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAP Business Object Cloud из коллекции.
2. Настройка и проверка единого входа Azure AD.

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>Добавление SAP Business Object Cloud из коллекции
Чтобы настроить интеграцию SAP Business Object Cloud с Azure AD, в коллекции необходимо добавить SAP Business Object Cloud в список управляемых приложений SaaS.

Чтобы добавить SAP Business Object Cloud из коллекции, сделайте следующее:

1. На [портале Azure](https://portal.azure.com) в меню слева щелкните **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Страница "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, выберите **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SAP Business Object Cloud**.

    ![Поле поиска](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. В области результатов выберите **SAP Business Object Cloud** и **Добавить**.

    ![SAP Business Object Cloud в списке результатов](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SAP Business Object Cloud с использованием тестового пользователя *Britta Simon*.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Microsoft Azure AD соответствует пользователю в SAP Business Object Cloud. Необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Business Object Cloud.

Для этого назначьте **имя пользователя** в Microsoft Azure AD в качестве значения **имени пользователя** в SAP Business Object Cloud.

Чтобы настроить и проверить единый вход Azure AD в SAP Business Object Cloud, выполните следующие задания:

1. [Настройка единого входа Azure AD](#set-up-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя SAP Business Object Cloud](#create-an-sap-business-object-cloud-test-user) требуется для создания пользователя Britta Simon в SAP Business Object Cloud, связанного с соответствующим пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) позволяет пользователю Britta Simon использовать единый вход Azure AD.
5. [Проверка единого входа](#test-single-sign-on) необходима, чтобы убедиться, что конфигурация работает правильно.

### <a name="set-up-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SAP Business Object Cloud.

Чтобы настроить единый вход Azure AD в SAP Business Object Cloud, сделайте следующее:

1. На портале Azure на странице интеграции с приложением **SAP Business Object Cloud** выберите **Единый вход**.

    ![Выбор параметра "Единый вход"][4]

2. На странице **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**.
 
    ![Выбор параметра "Вход на основе SAML"](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SAP Business Object Cloud** сделайте следующее:

    1. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. В поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![Домены и URL-адреса приложения SAP Business Object Cloud](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > Значения этих URL-адресов приведены только в качестве примера. Замените эти значения фактическим URL-адресом для входа и URL-адресом идентификатора. Чтобы получить URL-адрес для входа, обратитесь в [службу поддержки клиентов SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html). Вы можете получить URL-адрес идентификатора при загрузке метаданных SAP Business Object Cloud из консоли администрирования. Это объясняется далее в руководстве. 

4. В разделе **Сертификат подписи SAML** выберите **XML метаданных**. Затем сохраните файл метаданных на компьютере.

    ![Выбор XML метаданных](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Щелкните **Сохранить**.

    ![Нажатие кнопки "Сохранить"](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. В другом окне браузера войдите на сайт SAP Business Object Cloud своей компании в качестве администратора.

7. Выберите **Меню** > **Система** > **Администрирование**.
    
    ![Выбор параметров "Меню", "Система" и "Администрирование"](./media/active-directory-saas-sapboc-tutorial/config1.png)

8. На вкладке **Безопасность** щелкните значок пера **Изменить**.
    
    ![Выбор значка пера "Изменить" на вкладке "Безопасность"](./media/active-directory-saas-sapboc-tutorial/config2.png)  

9. В качестве **метода проверки подлинности** выберите **SAML Single Sign-On (SSO)** (Единый вход SAML (SSO)).

    ![Выбор параметра SAML Single Sign-On (SSO) (Единый вход SAML (SSO)) в качестве метода проверки подлинности](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Нажмите кнопку **Загрузить** для загрузки метаданных поставщика услуг (шаг 1). В файле метаданных найдите и скопируйте значение **EntityID** и вставьте его в поле **Идентификатор** на портале Azure в разделе **Домены и URL-адреса приложения SAP Business Object Cloud**.

    ![Копирование и вставка значения EntityID](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Чтобы отправить метаданные поставщика услуг (шаг 2) в файл, загруженный с портала Azure, в разделе **Upload Identity Provider metadata** (Отправить метаданные поставщика удостоверений) нажмите кнопку **Отправить**.  

    ![Нажатие кнопки "Отправить" в разделе Upload Identity Provider metadata (Отправить метаданные поставщика удостоверений)](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. В списке **Атрибут пользователя** выберите атрибут пользователя (шаг 3), который хотите использовать в своей реализации. Этот атрибут пользователя сопоставляется с поставщиком удостоверений. Чтобы ввести пользовательский атрибут на странице пользователя, используйте параметр **Custom SAML Mapping** (Пользовательское сопоставление SAML). Или выберите **Email** или **USER ID** в качестве пользовательского атрибута. В нашем примере выбран **Email**, так как мы сопоставили утверждение идентификатора пользователя с атрибутом **userprincipalname** в разделе **Атрибуты пользователя** на портале Azure. Так вы получаете уникальный адрес электронной почты пользователя, который отправляется в приложение SAP Business Object Cloud в каждом успешном ответе SAML.

    ![Выбор атрибута пользователя](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Чтобы проверить учетную запись с помощью поставщика удостоверений (шаг 4), в поле **Login Credential (Email)** (Учетные данные входа (электронная почта)) введите адрес электронной почты пользователя. Выберите **Проверить учетную запись**. Система добавит учетные данные входа в учетную запись пользователя.

    ![Ввод электронного адреса и нажатие кнопки "Проверить учетную запись"](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Щелкните значок **Сохранить**.

    ![Значок "Сохранить"](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Краткую версию этих инструкций можно также прочесть на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход**. Откройте встроенную документацию в разделе **Настройка** в нижней части страницы. Чтобы узнать больше, ознакомьтесь со [встроенной документацией по Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на портале Azure тестового пользователя с именем Britta Simon.

Чтобы создать тестового пользователя в Azure AD, сделайте следующее:

1. На портале Azure в меню слева щелкните **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Чтобы открыть список пользователей, выберите **Пользователи и группы**, а затем — **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. В диалоговом окне **Пользователь** сделайте следующее:
 
    1. В поле **Имя** введите **BrittaSimon**.

    2. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    3. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    4. Нажмите кнопку **Создать**.

        ![Диалоговое окно "Пользователь"](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    ![Создание пользователя Azure AD][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>Создание тестового пользователя SAP Business Object Cloud

Пользователей Azure AD нужно подготовить в SAP Business Object Cloud, прежде чем они смогут входить в SAP Business Object Cloud. В SAP Business Object Cloud подготовка выполняется вручную.

Чтобы подготовить учетную запись пользователя, сделайте следующее:

1. Выполните вход на корпоративный сайт SAP Business Object Cloud с правами администратора.

2. Выберите **Меню** > **Безопасность** > **Пользователи**.

    ![Добавление сотрудника](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. На странице **Пользователи** щелкните **+**, чтобы добавить сведения о новом пользователе. 

    ![Страница добавления пользователей](./media/active-directory-saas-sapboc-tutorial/user4.png)

    Затем сделайте следующее:

    1. В поле **ИД пользователя** введите идентификатор пользователя, например **Britta**.

    2. В текстовом поле **Имя** введите имя пользователя, например **Britta**.

    3. В текстовом поле **Фамилия** введите фамилию пользователя, например **Simon**.

    4. В текстовом поле **Отображаемое имя** введите полное имя пользователя, например **Britta Simon**.

    5. В текстовом поле **Электронная почта** введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    6. На странице **выбора ролей** выберите соответствующую роль для пользователя и нажмите кнопку **ОК**.

      ![Выбрать роль](./media/active-directory-saas-sapboc-tutorial/user3.png)

    7. Щелкните значок **Сохранить**.    


### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure AD, предоставив доступ учетных записей пользователей к SAP Business Object Cloud.

Чтобы назначить пользователя Britta Simon приложению SAP Business Object Cloud, сделайте следующее:

1. На портале Azure откройте представление приложений и перейдите к представлению каталога. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP Business Object Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Выбор параметра "Пользователи и группы"][202] 

4. Выберите **Добавить**. Затем на странице **Добавление назначения** выберите **Пользователи и группы**.

    ![Страница "Добавление назначения"][203]

5. На странице **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. На странице **Пользователи и группы** щелкните **Выбрать**.

7. На странице **Добавление назначения** выберите **Назначить**.

![Назначение роли пользователя][200] 
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP Business Object Cloud на панели доступа, вы автоматически войдете в приложение SAP Business Object Cloud.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png

