---
title: "Руководство по интеграции Azure Active Directory с Atlassian Cloud | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в приложении Atlassian Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: db9e9c7ae8380612bac9d0aeaaaf6df78cba523f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Руководство по интеграции Azure Active Directory с Atlassian Cloud

В этом руководстве описано, как интегрировать приложение Atlassian Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Atlassian Cloud обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Atlassian Cloud.
- Можно разрешить пользователям подписываться автоматически (единый вход) Atlassian облако с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно, через портал Azure.

Дополнительные сведения о программное обеспечение как услуга (SaaS) интеграции приложений с Azure AD см. в разделе [доступ к приложению и единый вход в Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с приложением Atlassian Cloud, вам потребуется:

- подписка Azure AD;
- Чтобы включить Security Assertion Markup Language (SAML) единого входа для облака Atlassian продуктов, необходимо Настройте диспетчер удостоверений. Дополнительные сведения о [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> При тестировании шаги в этом учебнике мы рекомендуем не использовать в производственной среде.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных стандартных блоков.

* Добавление Atlassian Cloud из коллекции
* настройка и проверка единого входа в Azure AD.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции
Для настройки интеграции Atlassian облака в Azure AD, добавьте Atlassian облака из коллекции в список управляемых приложений SaaS следующим образом:

1. На [портале Azure](https://portal.azure.com) в области слева нажмите кнопку **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Область "Корпоративные приложения"][2]
    
3. Чтобы добавить приложение, выберите **новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **облака Atlassian**, в списке результатов выберите **облака Atlassian**и выберите **добавить**.

    ![Atlassian Cloud в списке результатов](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе, настройки и тестирования в Azure AD единого входа с Atlassian, на основе облака для тестового пользователя с именем *Britta Simon*.

Azure AD для единого входа для работы, необходимо определить Atlassian облачных пользователей и их аналоги в Azure AD. Другими словами необходимо установить связи между пользователя Azure AD и соответствующих пользователей в облаке Atlassian.

Для установления связи, назначить в качестве облачной Atlassian *Username* то же значение, присвоенное Azure AD *имя пользователя*.

Для настройки и проверки Azure AD единого входа с облаком Atlassian, необходимо выполнить стандартные блоки в следующих разделах.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале и настроить его в приложении Atlassian Cloud.

Чтобы настроить Azure AD единого входа с облаком Atlassian, выполните следующее:

1. На портале Azure в **облака Atlassian** приложения интеграции выберите **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В **единого входа** окна в **режим-** выберите **входа на базе SAML**.
 
    ![Окно-](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Чтобы настроить приложение в режиме инициированные поставщика Удостоверений, в разделе **URL-адреса и домена облака Atlassian**, выполните следующие действия:

    ![Домен компании Atlassian облака и URL-адреса единого входа сведения](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. В **идентификатор** введите  **`https://auth.atlassian.com/saml/<unique ID>`** .
    
    Б. В **URL-адрес ответа** введите  **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`** .

    c. В **состояние ретрансляции** введите URL-адрес, используя следующий синтаксис:  **`https://<instancename>.atlassian.net`** .

4. Чтобы настроить приложение в режиме, инициированные SP, выберите **Показывать дополнительные параметры URL-адреса** и затем в **URL-адрес входа** введите URL-адрес, используя следующий синтаксис:  **`https://<instancename>.atlassian.net`**  .

    ![Домен компании Atlassian облака и URL-адреса единого входа сведения](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE] 
    > Приведенные выше значения используются только для примера. Обновите их с фактический идентификатор, URL-адрес ответа и URL-адрес входа. На экране настройки SAML Atlassian облака можно получить реальные значения. Далее в этом учебнике рассказывается значения.

5. В разделе **сертификат подписи SAML**выберите **Certificate(Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Atlassian облачного приложения ожидает найти утверждения SAML в определенном формате, поэтому необходимо добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. 

    По умолчанию **идентификатор пользователя** значение сопоставляется с user.userprincipalname. Измените это значение для сопоставления с user.mail. Можно также выбрать другие соответствующее значение в соответствии с вашей организации установки, но в большинстве случаев должна работать по электронной почте.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. Щелкните **Сохранить**.

    ![Настройка единого входа кнопку Сохранить](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. Чтобы открыть **настройки единого входа** окна в **конфигурации облака Atlassian** выберите **Настройка облака Atlassian**. 

9. В **краткий справочник** скопируйте **идентификатор сущности SAML** и **SAML единого входа URL-адрес службы**. 

    ![Конфигурация облака Atlassian](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

10. Для получения единого входа, настроенному для вашего приложения, войдите на портал компании Atlassian с учетными данными администратора.

11. Последовательно выберите пункты **Администрирование веб-узла компании Atlassian** > **организации и безопасность**. Если это еще не сделано, создайте и имя вашей организации и выберите в левой области **домены**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

12. Выберите, как вы хотите проверить домен: **DNS** или **HTTPS**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

13. Для проверки DNS в **домены** выберите **DNS** , а затем выполните следующее:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Чтобы скопировать значение для записи текста (TXT-запись), выберите **копирования**.

    Б. Чтобы добавить запись, перейдите на страницу параметров в DNS.

    c. Выберите параметр для добавления новой записи, а затем вставьте значение, которое вы скопировали в **домены** окна **значение** поле. Ваша запись DNS также может ссылаться на него как **ответов** или **описание**.

    d. Запись DNS также может включать в себя следующие поля:
    
    * В **тип записи** введите **TXT**.
    * В **узла/имя или псевдоним** оставьте значение по умолчанию (@ или пустое).
    * В **срок жизни (TTL)** введите **86400**.
    
    д.  Сохраните запись.

14. Вернитесь к **домены** окно управления организацией, а затем выберите **Проверка домена**. В **домена** введите имя домена и затем выберите **Проверка домена**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > Он может занять до 72 часов для изменения записей TXT вступили в силу, не будет известно сразу успешности подтверждения вашего домена. Чтобы просмотреть состояние проверки, проверьте **домены** окна вскоре после завершения этой процедуры. Новое состояние отображается как *проверено*, как показано на следующем рисунке:
    > 
    > ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)
    > 
    > 

15. Для проверки HTTPS в **домены** выберите **HTTPS** , а затем выполните следующее:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a. Чтобы загрузить файл HTML, выберите **Загрузите файл**.

    Б. Отправьте HTML-файл в корневой каталог домена.

16. Вернитесь к **домены** в организации администрирования и выберите **Проверка домена**. В **Проверка домена** окна в **домена** введите вашей **доменное имя**, а затем выберите **Проверка домена**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

17. Если в процессе проверки можно найти файл, загруженный в корневом каталоге, состояние домена обновляется до *проверено*, как показано ниже:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Дополнительные сведения см. в разделе [проверки домена Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

18. В левой области выберите **SAML единого входа**. Если это еще не сделано, подписаться на Atlassian Identity Manager.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

19. В **конфигурация SAML, добавьте** окно, выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. В **поставщика удостоверений идентификатор сущности** вставьте идентификатор сущности SAML, скопированный из портала Azure.

    Б. В **поставщика удостоверений URL-адрес SSO** вставьте URL-адрес службы единого входа SAML, скопированный из портала Azure.

    c. Откройте загруженный сертификат на портале Azure в txt-файл, скопируйте значение (без *начать сертификат* и *конечный сертификат* строк) и вставьте его в **открытый X509 сертификат** поле.
    
    d. Выберите **сохранить конфигурацию**.
     
20. Чтобы убедиться, что вы настроили правильные URL-адреса, измените параметры Azure AD, следующим образом:
  
    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. В окне SAML скопировать **идентификатор удостоверений SP** и затем на портале Azure в облаке Atlassian **URL-адреса и домена**, вставьте его в **идентификатор** поле.
    
    Б. В окне SAML скопировать **URL-адрес службы потребителя утверждение SP** и затем на портале Azure в облаке Atlassian **URL-адреса и домена**, вставьте его в **URL-адрес ответа** поле.  
        URL-адрес входа является URL-адрес клиента Atlassian облака. 

    > [!NOTE]
    > Если вы являетесь существующим клиентом, после обновления **идентификатор удостоверений SP** и **URL-адрес службы потребителя утверждение SP** значения на портале Azure выберите **Да, обновить конфигурацию**. Если вы нового клиента, этот шаг можно пропустить. 
    
21. На портале Azure выберите **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Как вы устанавливаете приложение, можно прочитать наглядный вариант предыдущего инструкций в [портал Azure](https://portal.azure.com). После добавления этого приложения из **Active Directory** > **корпоративных приложений** выберите **Single Sign-On** вкладку, а затем открыть внедренный Документация в **конфигурации** в нижней части окна. Чтобы узнать больше, ознакомьтесь со [встроенной документацией по Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе вы создадите на портале Azure тестового пользователя Britta Simon, выполнив следующие действия.

   ![Создание тестового пользователя Azure AD][100]

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Чтобы отобразить список пользователей, выберите **Пользователи и группы** > **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. В **всех пользователей** выберите **добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. В **пользователя** окно, выполните следующие действия:

    ![Окно пользователя](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Создание тестового пользователя в Atlassian Cloud

Чтобы пользователи Azure AD могли входить Atlassian облако, Подготовка учетных записей пользователей в облаке Atlassian вручную, выполнив следующие:

1. В **администрирования** выберите **пользователей**.

    ![Ссылка Atlassian облачных пользователей](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Чтобы создать пользователя в облаке Atlassian, выберите **пригласить пользователя**.

    ![Создать пользователя Atlassian облака](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. В **адрес электронной почты** введите адреса электронной почты пользователя и затем назначить доступ к приложениям. 

    ![Создать пользователя Atlassian облака](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Чтобы отправить по электронной почте приглашение для пользователя, установите **приглашения пользователей**.  
    Приглашение по электронной почте отправляется пользователю, и после принятия приглашения, активные в системе пользователя. 

>[!NOTE] 
>Возможно также массовое-создавать пользователей, выбирая **массового создания** кнопку в **пользователей** раздела.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите пользователя Саймон Britta для использования Azure единого входа, предоставление доступа к компании Atlassian облака. Для этого выполните следующее.

![Назначение роли пользователя][200] 

1. На портале Azure откройте **приложений** просмотра, перейдите в представление каталога и выберите **корпоративных приложений** > **все приложения**.

    ![Назначение пользователя][201] 

2. В **приложений** выберите **Atlassian облака**.

    ![Ссылка на Atlassian Cloud в списке приложений](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. В области слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**, затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В **пользователей и групп** окна в **пользователей** выберите **Britta Simon**.

6. В **пользователей и групп** выберите **выберите**.

7. В **добавить назначение** выберите **назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При выборе **Atlassian облака** плитки на панели доступа, вы должны выполнять вход автоматически в Atlassian облачного приложения.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

