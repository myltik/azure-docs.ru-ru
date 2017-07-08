---
title: "Приступая к работе с Azure AD версии 2 для классического приложения для Windows. Настройка | Документация Майкрософт"
description: "Здесь описывается, как классические приложения для Windows .NET (XAML) могут вызвать API, требующий маркеры доступа от конечной точки Azure Active Directory версии 2."
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: be00cb8f117fcbd2d137197f7dbe9713540cea61
ms.contentlocale: ru-ru


---

## <a name="set-up-your-project"></a>Настройка проекта

В этом разделе содержатся пошаговые инструкции по созданию проекта, чтобы продемонстрировать, как интегрировать классическое приложение для Windows .NET (XAML) с *входом с учетной записью Майкрософт*, что позволит выполнять запрос к веб-API, требующим маркер.

Приложение, созданное в этом руководстве, предоставляет кнопку для графа, а также отображает результаты на экране и кнопку выхода.

> Предпочитаете скачать этот пример проекта Visual Studio? [Скачайте проект](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip) и перейдите к [настройке](#create-an-application-express "Configuration Step"), чтобы настроить пример кода перед выполнением.


### <a name="create-your-application"></a>Создание приложения
1. В Visual Studio выберите `File` > `New` > `Project`.<br/>
2. В разделе *Шаблоны* выберите `Visual C#`.
3. Выберите `WPF App` (или *приложение WPF* в зависимости от версии Visual Studio).

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Добавление библиотеки проверки подлинности Майкрософт (MSAL) в проект
1. В Visual Studio выберите `Tools` > `Nuget Package Manager` > `Package Manager Console`.
2. Скопируйте и вставьте следующий код в окне "Консоль диспетчера пакетов".

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

> Приведенный выше пакет устанавливает библиотеку проверки подлинности Майкрософт (MSAL). MSAL обрабатывает получение, кэширование и обновление маркеров пользователей, которые используются для доступа к API, защищенному конечной точкой Azure Active Directory версии 2.

## <a name="add-the-code-to-initialize-msal"></a>Добавление кода для инициализации MSAL
На этом шаге вы сможете создать класс для обработки взаимодействия с библиотекой MSAL, например обработки маркеров.

1. Откройте файл `App.xaml.cs` и добавьте справочник по библиотеке MSAL в класс:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Обновите класс App следующим образом:
</li>
</ol>

```csharp
public partial class App : Application
{
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";

    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);

}
```

## <a name="create-your-applications-ui"></a>Создание пользовательского интерфейса приложения
В разделе ниже описывается, как приложение может запрашивать защищенный внутренний сервер, например Microsoft Graph. Файл MainWindow.xaml должен создаваться автоматически как часть шаблона проекта. Откройте этот файл и выполните приведенные далее инструкции.

1.  Вставьте в раздел `<Grid>` приложения следующий код:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

