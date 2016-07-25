<properties
    pageTitle="Руководство. Интеграция Azure Active Directory с ABa Sainsburys Connect | Microsoft Azure" 
    description="Узнайте, как использовать ABa Sainsburys Connect вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#Руководство. Интеграция Azure Active Directory с ABa Sainsburys Connect

Цель данного учебника — показать интеграцию Azure и Aba Sainsburys Connect. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Подписка с поддержкой единого входа Aba Sainsburys Connect

После завершения этого руководства пользователи Azure AD, назначенные Aba Sainsburys Connect, будут иметь возможность единого входа в приложение на веб-сайте компании Aba Sainsburys Connect (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для Aba Sainsburys Connect
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "Сценарий")
##Включение интеграции приложений для Aba Sainsburys Connect

В этом разделе показано, как включить интеграцию приложений для Aba Sainsburys Connect.

###Чтобы включить интеграцию приложений для Aba Sainsburys Connect, выполните следующие действия.

1.  На классическом портале Azure в области навигации слева щелкните **Active Directory**.

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **Aba Sainsburys Connect**.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "Aba Sainsburys Connect")

7.  В области результатов выберите **Aba Sainsburys Connect** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "Aba Sainsburys Connect")
##Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Aba Sainsburys Connect со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

###Чтобы настроить единый вход, выполните следующие действия.

1.  На странице интеграции с приложением **Aba Sainsburys Connect** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в Aba Sainsburys Connect?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "Настройка единого входа")

3.  На странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка параметров приложения](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "Настройка параметров приложения")

    1.  В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Aba Sainsburys Connect (например, *https://myaba.co.uk/client-access/sainsburys/saml.php*).
    2.  Нажмите кнопку **Далее**.

4.  На странице **Настройка единого входа в Aba Sainsburys Connect** нажмите кнопку **Загрузить метаданные** для скачивания метаданных, а затем сохраните файл данных на свой компьютер.

    ![Настройка единого входа](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "Настройка единого входа")

5.  Отправьте загруженный файл метаданных в службу поддержки Aba Sainsburys Connect.

    >[AZURE.NOTE] Служба поддержки Aba Sainsburys Connect произведет настройку SSO. Как только единый вход для вашей подписки будет включен, вы получите уведомление.

6.  На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "Настройка единого входа")
##Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Aba Sainsburys Connect, они должны быть подготовлены для Aba Sainsburys Connect. В случае Aba Sainsburys Connect учетные записи пользователей должны создаваться службой поддержки Aba Sainsburys Connect.

>[AZURE.NOTE] Вы можете использовать любые другие средства создания учетных записей пользователя Aba Sainsburys Connect или API, предоставляемые Aba Sainsburys Connect для подготовки учетных записей пользователей Azure Active Directory.

##Назначение пользователей

Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

###Чтобы назначить пользователей Aba Sainsburys Connect, выполните следующие действия.

1.  На классическом портале Azure создайте тестовую учетную запись.

2.  На странице интеграции с приложением **Aba Sainsburys Connect** нажмите кнопку **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->