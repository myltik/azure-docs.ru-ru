---
title: "Руководство по интеграции Azure Active Directory с SAP Business Object Cloud | Документы Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в SAP Business Object Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: a09e965b6fc9b89023c09092860fcf79773a4518
ms.contentlocale: ru-ru
ms.lasthandoff: 08/12/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Руководство по интеграции Azure Active Directory с SAP Business Object Cloud

В этом руководстве описано, как интегрировать SAP Business Object Cloud с Azure Active Directory (Azure AD).

Интеграция SAP Business Object Cloud с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению SAP Business Object Cloud.
- Вы можете включить автоматический вход пользователей в SAP Business Object Cloud (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SAP Business Object Cloud, вам потребуется:

- подписка Azure AD;
- подписка SAP Business Object Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SAP Business Object Cloud из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Добавление SAP Business Object Cloud из коллекции
Чтобы настроить интеграцию SAP Business Object Cloud с Azure AD, необходимо добавить SAP Business Object Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Business Object Cloud из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **SAP Business Object Cloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. На панели результатов выберите **SAP Business Object Cloud** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в SAP Business Object Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP Business Object Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Business Object Cloud.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP Business Object Cloud.

Чтобы настроить и проверить единый вход Azure AD в SAP Business Object Cloud, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP Business Object Cloud](#creating-an-sap-business-object-cloud-test-user)** требуется для создания пользователя Britta Simon в SAP Business Object Cloud, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SAP Business Object Cloud.

**Чтобы настроить единый вход Azure AD в SAP Business Object Cloud, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **SAP Business Object Cloud** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SAP Business Object Cloud** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Обратитесь в [службу поддержки клиентов SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html) для получения URL-адрес входа. Вы можете получить идентификатор при загрузке метаданных SAP Business Object Cloud на консоли администратора — эта процедура будет рассмотрена ниже в этом учебнике. 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на сайт SAP Business Object Cloud своей компании в качестве администратора.

7. Последовательно выберите **Меню > Система > Администрирование**
    
    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. На вкладке **Безопасность** щелкните **[Правка — значок пера]**.
    
    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. В качестве метода проверки подлинности выберите **Единый вход SAML (SSO)**.

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Нажмите кнопку **Загрузить** для загрузки метаданных поставщика услуг. Получите значение **entityID** из файла и вставьте его в текстовое поле **Идентификатор** в разделе **Домен и URL-адреса SAP Business Object Cloud** на портале Azure.

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. На шаге **Отправить метаданные поставщика удостоверений** нажмите кнопку **Отправить...** для отправки файла метаданных, загруженного с портала Azure. 

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Выберите соответствующий **атрибут пользователя**, который нужно использовать для своей реализации из списка для сопоставления с поставщиком удостоверений. Используйте параметр "Пользовательское сопоставление SAML" для ввода настраиваемого атрибута на странице "пользователи" или выберите "Email" или "USER ID" в качестве пользовательского атрибута. Пример. В данном случае выбран **Email**, так как мы сопоставили утверждение идентификатора пользователя с атрибутом userprincipalname в разделе **"Атрибуты пользователя"** на портале Azure, предоставляющим уникальный адрес электронной почты пользователя, который отправляется в приложение SAP Business Object Cloud в каждом успешном ответе SAML.

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Введите свой ваш адрес электронной почты в текстовом поле **Учетные данные входа (электронная почта)** и нажмите кнопку **Проверить учетную запись**, чтобы система смогла добавить учетные данные входа в учетную запись.

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Щелкните значок **Сохранить**.

    ![Сохранить](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>Создание тестового пользователя SAP Business Object Cloud

Чтобы пользователи Azure AD могли выполнять вход в SAP Business Object Cloud, они должны быть подготовлены в SAP Business Object Cloud. В случае SAP Business Object Cloud подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на корпоративный сайт SAP Business Object Cloud с правами администратора.

2. Последовательно выберите пункты **Меню > Безопасность > Пользователи**

    ![Добавление сотрудника](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. На странице **Пользователи** щелкните **"+"** для добавления сведений о новом пользователе и выполните следующие действия:

    ![Приглашение пользователей](./media/active-directory-saas-sapboc-tutorial/user4.png)

    а. В текстовом поле **ИД ПОЛЬЗОВАТЕЛЯ** введите идентификатор пользователя, например Britta.

    b. В текстовом поле **Имя** введите имя, допустим, Britta.

    c. В текстовом поле **Фамилия** введите имя, допустим, Simon.

    г) В текстовом поле **Отображаемое имя** введите полное имя пользователя, например Britta Simon.

    д. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    f. Выберите соответствующую роль для пользователя.

      ![Роль](./media/active-directory-saas-sapboc-tutorial/user3.png)

    ж. Щелкните значок **Сохранить**. 


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к SAP Business Object Cloud.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SAP Business Object Cloud, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP Business Object Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SAP Business Object Cloud на панели доступа, вы автоматически войдете в приложение SAP Business Object Cloud.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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


