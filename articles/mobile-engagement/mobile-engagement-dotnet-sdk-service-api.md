---
title: Использование пакета SDK для .NET для доступа к интерфейсам API Служб мобильного взаимодействия Azure
description: Описывает, как использовать пакет SDK Служб мобильного взаимодействия для .NET для доступа к интерфейсам API Служб мобильного взаимодействия Azure
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: c07728aa-43f2-4238-8b4a-c9eddf9d838b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4a303db638b8ea304db5cba4075b0b102eedd0f3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>Использование пакета SDK для .NET для доступа к интерфейсам API Служб мобильного взаимодействия Azure
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Службы мобильного взаимодействия Azure предоставляют набор интерфейсов API для управления устройствами, кампаниями охвата/продвижения и т. д. Для взаимодействия с этими интерфейсами API мы также предоставляем [файл Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json), который можно использовать со средствами для создания пакетов SDK для предпочитаемого языка. Мы рекомендуем использовать средство [AutoRest](https://github.com/Azure/AutoRest) для создания пакета SDK из нашего файла Swagger.

> [!NOTE]
> Мы прекратим использование Служб мобильного взаимодействия Azure в марте 2018 г. Сейчас они доступны только существующим клиентам. Дополнительные сведения см. на странице [Службы мобильного взаимодействия](https://azure.microsoft.com/services/mobile-engagement/).

Аналогичным образом мы создали пакет SDK для .NET, который позволяет взаимодействовать с этими интерфейсами API с помощью оболочки C# без самостоятельного согласования маркера проверки подлинности и обновления.  

В этом примере используется набор шагов, которые необходимо выполнить для использования пакета SDK для .NET:

1. Во-первых, необходимо настроить проверку подлинности для интерфейсов API с помощью Azure Active Directory, как описано [здесь](mobile-engagement-api-authentication.md#authentication). По завершении этих шагов вы должны иметь допустимые **идентификатор подписки**, **идентификатор клиента**, **идентификатор приложения** и **секрет**. 
2. Мы используем простое консольное приложение Windows для демонстрации работы с пакетом SDK для .NET с помощью сценария создания кампании типа "Объявление". Откройте Visual Studio и создайте **консольное приложение**.   
3. Далее необходимо загрузить пакет SDK для .NET, который доступен в виде **библиотеки управления Microsoft Azure Engagement** в коллекции Nuget [здесь](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/).
   Если вы устанавливаете Nuget из Visual Studio, убедитесь, что при поиске пакета установлен флажок **Включить предварительный выпуск** :
   
    ![][1]
4. В файле `Program.cs` добавьте следующие пространства имен:
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;
5. Затем необходимо определить следующие константы, которые будут использоваться для проверки подлинности и взаимодействия с приложением Служб мобильного взаимодействия, в котором вы создаете кампанию типа "Объявление":
   
        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";
   
        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";
   
        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";
6. Определите переменную `EngagementManagementClient`, которая будет использоваться для вызова методов пакета SDK для Служб мобильного взаимодействия:
   
        static EngagementManagementClient engagementClient; 
7. Затем добавьте в метод `Main` следующие строки:
   
        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();
   
                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }
8. Определите следующий метод, который отвечает за инициализацию `EngagementManagementClient`, выполняя проверку подлинности и связывая себя с приложением Служб мобильного взаимодействия, в котором вы планируете создать кампанию типа "Объявление":
   
        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
   
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;
   
            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }
   
   > [!IMPORTANT]
   > Обратите внимание, что для параметра AppName необходимо использовать **имя ресурса приложения** , определенное в портале управления Azure. 
   > 
   > 
9. Наконец, определите метод CreateCampaign, который с помощью ранее инициализированного объекта EngagementClient создаст простую кампанию с характеристиками **любое время** & **Только уведомление** и со следующим заголовком и сообщением: 
   
        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );
   
            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }
10. Запустите консольное приложение, и при успешном создании кампании вы увидите следующее сообщение:
    
    **Кампания с идентификатором 159 успешно создана и находится в состоянии "черновик"**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
