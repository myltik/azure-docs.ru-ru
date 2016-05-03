<properties 
   pageTitle="Добавление Azure Active Directory с помощью подключенных служб в Visual Studio | Microsoft Azure"
   description="Добавление Azure Active Directory с помощью диалогового окна ";Добавление подключенных служб"; в Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="visual-studio-online"
   ms.date="04/18/2016"
   ms.author="tarcher" />

# Добавление Azure Active Directory с помощью подключенных служб в Visual Studio 

##Обзор
Azure Active Directory (Azure AD) позволяет обеспечить единый вход для веб-приложений ASP.NET MVC или проверку подлинности AD в службах веб-API. С проверкой подлинности Azure AD пользователи смогут подключаться к вашим веб-приложениям, используя свои учетные записи Azure AD. В число преимуществ проверки подлинности Azure AD с веб-API входит усиленная защита данных при использовании API из веб-приложения. С Azure AD вам не придется управлять отдельной системой проверки подлинности с отдельным управлением пользователями и учетными записями.

## Поддерживаемые типы проектов

С помощью диалогового окна подключенных служб можно подключаться к Azure AD в проектах следующих типов:

- Проекты ASP.NET MVC

- Проекты веб-API ASP.NET


### Подключение к Azure AD с помощью диалогового окна подключенных служб

1. Убедитесь в наличии учетной записи Azure. Если у вас ее нет, вы можете зарегистрироваться и получить [бесплатную пробную версию](http://go.microsoft.com/fwlink/?LinkId=518146).

1. В Visual Studio откройте контекстное меню узла **Ссылки** в своем проекте и выберите пункт **Добавить подключенные службы**.
1. Выберите **проверку подлинности Azure AD** и нажмите кнопку **Настроить**.

    ![Выберите "Добавить проверку подлинности Azure AD"](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. На первой странице мастера **Настройка проверки подлинности Azure AD** установите флажок **Настроить единый вход с помощью Azure AD**.

    Если в проекте настроена другая конфигурация проверки подлинности, мастер предупредит, что продолжение его работы приведет к отключению предыдущей конфигурации.

    ![Настройка Azure AD с помощью мастера](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  На второй странице выберите домен из раскрывающегося списка **Домены**. Список доменов включает все домены, доступные для учетных записей, которые указаны в диалоговом окне параметров учетной записи. Если вы не нашли искомый домен, введите имя домена, например mydomain.onmicrosoft.com, вручную. Вы можете выбрать параметр для создания нового приложения Azure AD или использовать параметры уже существующего приложения Azure AD.

    ![Настройка Azure AD с помощью мастера](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. На третьей странице мастера убедитесь в том, что установлен флажок **Чтение данных каталога**. Мастер заполнит **секрет клиента**.

    ![Настройка Azure AD с помощью мастера](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Нажмите кнопку **Готово**. Диалоговое окно добавляет код конфигурации и ссылки, необходимые для включения проверки подлинности Azure AD в вашем проекте. Домен AD появится на портале Azure.

1. Просмотрите открывшуюся в браузере страницу «Приступая к работе». Здесь приведена информация о дальнейших действиях. Сведения о том, какие изменения внесены в ваш проект, приведены в статье «Что произошло». Если вы хотите убедиться, что все сработало, откройте один из измененных файлов конфигурации и убедитесь, что в нем есть все параметры, упомянутые в статье «Что произошло». Например, в основной файл web.config проекта ASP.NET MVC будут добавлены следующие параметры:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## Какие изменения произойдут в проекте

При запуске мастера Visual Studio добавляет в проект Azure AD и соответствующие ссылки. В файлы конфигурации и файлы кода в проекте вносятся изменения, обеспечивающие поддержку Azure AD. Конкретные изменения, производимые Visual Studio, зависят от типа вашего проекта. Подробные сведения о том, как изменяются проекты ASP.NET MVC, см. в статье [Что произошло: проекты MVC](http://go.microsoft.com/fwlink/p/?LinkID=513809). Эти же сведения о проектах веб-API см. в статье [Что произошло: проекты веб-API](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##Дальнейшие действия

Задавайте вопросы и получайте справку:

 - [Форум MSDN: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Документация по Azure AD](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Записи блога: общие сведения об Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=AcomDC_0420_2016-->