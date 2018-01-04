---
title: "Руководство по интеграции Azure Active Directory с решением SilkRoad Life Suite | Документация Майкрософт"
description: "Узнайте, как настроить единый вход для Azure Active Directory и SilkRoad Life Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0d6af7af7d6b28ff3ea9d518a65b8267a83b71b4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Руководство по интеграции Azure Active Directory с решением SilkRoad Life Suite

В этом учебнике вы узнаете, как интегрировать SilkRoad жизни набора с Azure Active Directory (Azure AD).

Интеграция SilkRoad Life Suite с Azure AD обеспечивает следующие преимущества:

- Можно управлять в Azure AD, кто имеет доступ к SilkRoad жизни набора.
- Вы можете включить их учетные записи Azure AD пользователям автоматически получить вошедшего в набор жизни SilkRoad (Single Sign-On).
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с SilkRoad Life Suite, вам потребуется следующее:

- подписка Azure AD;
- подписка SilkRoad Life Suite с активированной функцией единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SilkRoad Life Suite из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Добавление SilkRoad Life Suite из коллекции.
Чтобы настроить интеграцию SilkRoad Life Suite в Azure AD, необходимо добавить SilkRoad Life Suite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SilkRoad Life Suite из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SilkRoad жизни набора**выберите **SilkRoad жизни набора** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![Набор SilkRoad жизни в списке результатов](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с набором жизни SilkRoad основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать пользователя аналога в наборе SilkRoad жизни для пользователя в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SilkRoad Life Suite.

В наборе SilkRoad жизни, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Чтобы настроить и проверить единый вход Azure AD в SilkRoad Life Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Suite жизни SilkRoad](#create-a-silkroad-life-suite-test-user)**  — иметь аналог Саймон Britta в наборе SilkRoad жизни, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении SilkRoad жизни набора.

**Чтобы настроить единый вход в Azure AD в SilkRoad Life Suite, выполните следующие действия:**

1. На портале Azure на **SilkRoad жизни набора** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. На **SilkRoad жизни набора домена и URL-адреса** выполните следующие действия:

    ![SilkRoad жизни набора домена и URL-адреса единого входа сведения](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.silkroad-eng.com/Authentication/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Обратитесь к [SilkRoad жизни набора клиентских поддержки](https://www.silkroad.com/locations/) для получения этих значений. 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. На **SilkRoad жизни набора конфигурации** щелкните **настроить набор жизни SilkRoad** Открытие **настройки единого входа** окна. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка пакета SilkRoad жизни](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Войдите на сайт компании SilkRoad от имени администратора. 
 
    >[!NOTE] 
    > Чтобы получить доступ к приложению SilkRoad Life Suite Authentication для настройки федерации с Microsoft Azure AD, обратитесь в службу поддержки компании SilkRoad или к представителю отдела обслуживания клиентов компании SilkRoad.

8. В разделе **Service Provider** (Поставщик услуг) щелкните **Federation Details** (Сведения о федерации). 
   
    ![единого входа Azure AD][10]

9. Щелкните **Download Federation Metadata**(Загрузить метаданные федерации), а затем сохраните файл метаданных на вашем компьютере.
   
    ![единого входа Azure AD][11] 

10. В приложении **SilkRoad** щелкните **Authentication Sources** (Источники проверки подлинности).
   
    ![единого входа Azure AD][12] 

11. Щелкните **Add Authentication Source**(Добавить источник аутентификации). 
   
    ![единого входа Azure AD][13] 

12. В разделе **Add Authentication Source** (Добавление источника аутентификации) выполните следующие действия: 
   
    ![единого входа Azure AD][14]
  
    a. В разделе **вариант 2 - файл метаданных**, нажмите кнопку **Обзор** Чтобы отправить скачанный файл метаданных из портала Azure.
  
    Б. Нажмите кнопку **Создать поставщик удостоверений с помощью данных из файла**.

13. В разделе **Authentication Sources** (Источники проверки подлинности) нажмите кнопку **Edit** (Изменить). 
    
     ![единого входа Azure AD][15] 

14. В диалоговом окне **Edit Authentication Source** (Изменение источника аутентификации) выполните следующие действия: 
    
     ![единого входа Azure AD][16] 

    a. В поле **Enabled** (Включено) выберите **Yes** (Да).

    Б. В **EntityId** текстовое поле, вставьте значение **идентификатор сущности SAML** скопирован из портала Azure.
   
    c. В **описание поставщика удостоверений** в текстовое поле введите описание для конфигурации (например: *единого входа Azure AD*).

    d. В **файл метаданных** текстового поля, передачи **метаданные** файл, который вы скачали из портала Azure.
  
    д. В **имя поставщика удостоверений** текстовом поле введите имя, которое относится к конфигурации (например: *Azure SP*).
  
    f. В **URL-адрес выхода службы** текстовое поле, вставьте значение **URL-адрес выхода** скопирован из портала Azure.

    ж. В **входа URL-адрес службы** текстовое поле, вставьте значение **SAML единого входа URL-адрес службы** скопирован из портала Azure.

    h. Выберите команду **Сохранить**.

15. Отключите все остальные источники аутентификации. 
    
     ![единого входа Azure AD][17]

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Создание тестового пользователя SilkRoad Life Suite

В этом разделе создайте пользователя с именем Britta Simon SilkRoad жизни набора. Работать с [SilkRoad жизни набора клиентских поддержки](https://www.silkroad.com/locations/) для добавления пользователей в платформе SilkRoad жизни набора. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к SilkRoad жизни набора.

![Назначение роли пользователя][200] 

**Чтобы назначить Britta Simon в SilkRoad Life Suite, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SilkRoad Life Suite**.

    ![Набор жизни SilkRoad ссылку в список приложений](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SilkRoad Life Suite на панели доступа, вы автоматически войдете в приложение SilkRoad Life Suite.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
