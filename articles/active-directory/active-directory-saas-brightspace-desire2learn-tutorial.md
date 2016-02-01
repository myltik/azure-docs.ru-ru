<properties 
    pageTitle="Руководство. Интеграция Azure Active Directory с Brightspace (разработка Desire2Learn) | Microsoft Azure" 
    description="Узнайте, как использовать Brightspace (разработка Desire2Learn) вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Руководство. Интеграция Azure Active Directory с Brightspace (разработка Desire2Learn)

Цель данного учебника — показать интеграцию Azure и Brightspace (разработка Desire2Learn). Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Подписка с поддержкой единого входа Brightspace (разработка Desire2Learn)

После завершения этого руководства пользователи Azure AD, назначенные Brightspace (разработка Desire2Learn), будут иметь возможность единого входа в приложение на веб-сайте компании Brightspace (разработка Desire2Learn; вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для Brightspace (разработка Desire2Learn)
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Сценарий")
##Включение интеграции приложений для Brightspace (разработка Desire2Learn)

В этом разделе показано, как включить интеграцию приложений для Brightspace (разработка Desire2Learn).

###Чтобы включить интеграцию приложений для Brightspace (разработка Desire2Learn), выполните следующие действия.

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **Brightspace (разработка Desire2Learn)**.

    ![Коллекция приложений](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Коллекция приложений")

7.  В области результатов выберите **Brightspace (разработка Desire2Learn)** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Brightspace (разработка Desire2Learn)](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace (разработка Desire2Learn)")
##Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Brightspace (разработка Desire2Learn) со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

###Чтобы настроить единый вход, выполните следующие действия.

1.  На странице интеграции с приложением **Brightspace (разработка Desire2Learn)** портала Azure AD нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в Brightspace (разработка Desire2Learn)?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Настройка единого входа")

3.  На странице **Настройка URL-адреса приложения** выполните следующие действия.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Настройка URL-адреса приложения")

    1.  В текстовом поле **URL-адрес входа** введите URL-адрес, используемый для входа в ваше приложение **Brightspace (разработка Desire2Learn)** (например, **https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*)).
2.  Нажмите кнопку **Далее**.

4.  На странице **Настройка единого входа в Brightspace (разработка Desire2Learn)** нажмите кнопку **Загрузить метаданные**, чтобы скачать их, а затем сохраните файл данных на свой компьютер.

    ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Настройка единого входа")

5.  Отправьте загруженный файл метаданных в службу поддержки Brightspace (разработка Desire2Learn).

    >[AZURE.NOTE]Настройка единого входа должна выполняться службой поддержки Brightspace (разработка Desire2Learn). Как только единый вход для вашей подписки будет включен, вы получите уведомление.

6.  На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Настройка единого входа")
##Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Brightspace (разработка Desire2Learn), они должны быть подготовлены для Brightspace (разработка Desire2Learn). В случае Brightspace (разработка Desire2Learn) учетные записи пользователей должны создаваться службой поддержки Brightspace (разработка Desire2Learn).

>[AZURE.NOTE]Вы можете использовать любые другие средства создания учетной записи пользователя Brightspace (разработка Desire2Learn) или API, предоставляемые Brightspace (разработка Desire2Learn) для подготовки учетных записей пользователя Azure Active Directory.

##Назначение пользователей

Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

###Чтобы назначить пользователей Brightspace (разработка Desire2Learn), выполните следующие действия.

1.  На портале Azure AD создайте тестовую учетную запись.

2.  На странице интеграции приложения **Brightspace (разработка Desire2Learn)** нажмите кнопку **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->