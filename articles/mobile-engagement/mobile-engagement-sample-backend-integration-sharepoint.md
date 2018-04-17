---
title: Службы мобильного взаимодействия Azure — интеграция с серверной частью
description: Подключите Службы мобильного взаимодействия Azure к серверной части SharePoint для создания кампаний из SharePoint.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 06297b43-579f-46e6-8a58-961a68f9aa09
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cf530ca6dce32050487f367aa80b10bf7f20b7e7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---api-integration"></a>Службы мобильного взаимодействия Azure — интеграция API
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

В автоматизированной системе маркетинга создание и активация маркетинговых кампаний также происходит автоматически. Поэтому Службы мобильного взаимодействия Azure поддерживают создание автоматических маркетинговых кампаний с помощью API. 

Как правило, для создания объявлений и опросов в рамках маркетинговых кампаний клиенты используют интерфейсную часть решений. Однако по мере того как маркетинговые кампании становятся более зрелыми, возникает необходимость использовать данные серверной части (например, из CRM или систем управления контентом, таких как SharePoint). Они позволяют полностью автоматизировать динамическое создание кампаний в Службах мобильного взаимодействия на основе данных, поступающих из серверных систем. 

![][5]

В этом учебнике рассматривается сценарий, в котором бизнес-пользователь SharePoint вносит маркетинговые данные в список SharePoint, а автоматизированный процесс извлекает элементы из списка и, используя интерфейсы API REST, подключается к системе Служб мобильного взаимодействия для создания маркетинговой кампании на основе данных SharePoint. 

> [!IMPORTANT]
> Вы можете использовать приведенный пример в качестве отправной точки для изучения принципов работы любого интерфейса API REST для Служб мобильного взаимодействия, так как здесь подробно рассматриваются два ключевых аспекта вызова API: проверка подлинности и передача параметров. 
> 
> 

## <a name="sharepoint-integration"></a>Интеграция с SharePoint
1. Вот как выглядит учебный список SharePoint. Для создания объявления используются параметры **Title** (Заголовок), **Category** (Категория), **NotificationTitle** (Заголовок уведомления), **Message** (Сообщение) и **URL** (URL-адрес). Также есть столбец **IsProcessed** (обработано), используемый нашим процессом автоматизации в консольной программе. Можно запустить консольную программу как веб-задание Azure (в этом случае вы сможете планировать ее запуск) или использовать рабочий процесс SharePoint напрямую для программного создания и активации объявлений при вставке элемента в список SharePoint. В этом примере мы используем консольную программу, которая перебирает элементы в списке SharePoint, создает объявление в Службах мобильного взаимодействия Azure для каждого из них, а затем устанавливает флаг **IsProcessed** для подтверждения успешно созданных объявлений.
   
    ![][1]
2. Для проверки подлинности при работе со списком SharePoint мы будем использовать код из примера *Удаленная проверка подлинности в SharePoint Online с помощью модели COM* [этой ссылке](https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c) .
3. После проверки подлинности мы зациклим перебор элементов списка, чтобы выявлять вновь добавленные элементы (флаг **IsProcessed** = false). 
   
         static async void CreateCampaignFromSharepoint()
        {
            using (ClientContext clientContext = ClaimClientContext.GetAuthenticatedContext(targetSharepointSite))
            {
                // Using https://code.msdn.microsoft.com/Remote-Authentication-in-b7b6f43c for authentication     
                Web site = clientContext.Web;
                List list = site.Lists.GetByTitle("VideoContent");
                CamlQuery query = new CamlQuery();
                query.ViewXml = "<View/>";
                ListItemCollection items = list.GetItems(query);
   
                // Load the SharePoint list
                clientContext.Load(list);
                clientContext.Load(items);
                clientContext.ExecuteQuery();
   
                // Loop through the list to go through all the items which are newly added
                foreach (ListItem item in items)
                    if (item["IsProcessed"].ToString() != "Yes")
                    {
                        string name = item["Title"].ToString();
                        string notificationTitle = item["NotificationTitle"].ToString();
                        string notificationMessage = item["Message"].ToString();
                        string category = item["Category"].ToString();
                        string actionURL = ((FieldUrlValue)item["URL"]).Url;
   
                        // Send an HTTP request to create AzME campaign
                        int campaignId = CreateAzMECampaign
                            (name, notificationTitle, notificationMessage, category, actionURL).Result;
   
                        if (campaignId != -1)
                        {
                            // If creating campaign is successful then set this to true
                            item["IsProcessed"] = "Yes";
   
                            // Now try to activate the campaign also
                            await ActivateAzMECampaign(campaignId);
                        }
                        else
                        {
                            item["IsProcessed"] = "Error";
                        }
                        item.Update();
                    }
                clientContext.ExecuteQuery();
            }
        }

## <a name="mobile-engagement-integration"></a>Интеграция со Службами мобильного взаимодействия
1. Когда мы найдем элемент списка, требующий обработки, мы извлечем из него сведения, необходимые для создания объявления, и вызовем `CreateAzMECampaign`, чтобы создать его, а затем `ActivateAzMECampaign` — чтобы активировать. Фактически это вызовы API REST к серверной части Служб мобильного взаимодействия. 
2. Для интерфейсов REST API для Служб мобильного взаимодействия требуется **HTTP-заголовок авторизации базовой схемы проверки подлинности**, состоящий из компонентов `ApplicationId` и `ApiKey`, которые можно найти на портале Azure. Убедитесь, что вы используете ключ из раздела **api keys** (а *не* из раздела **sdk keys**). 
   
   ![][2]
   
       static string CreateAuthZHeader()
       {
           string BasicAuthzHeaderString = "Basic " + EncodeTo64(ApplicationId + ":" + ApiKey);
           return BasicAuthzHeaderString;
       }
   
       static public string EncodeTo64(string toEncode)
       {
           byte[] toEncodeAsBytes = System.Text.ASCIIEncoding.ASCII.GetBytes(toEncode);
           string returnValue = System.Convert.ToBase64String(toEncodeAsBytes);
           return returnValue;
       }  
3. Сведения о создании кампаний с типом announcement (Объявление) см. в [документации](https://msdn.microsoft.com/library/azure/mt683750.aspx). Убедитесь, что для параметра `kind` кампании вы задаете значение *announcement* и передаете его вместе с [дополнительными параметрами](https://msdn.microsoft.com/library/azure/mt683751.aspx) в виде FormUrlEncodedContent. 
   
        static async Task<int> CreateAzMECampaign(string campaignName, string notificationTitle, 
            string notificationMessage, string notificationCategory, string actionURL)
        {
            string createURIFragment = "/reach/1/create";
   
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                // Create the payload to send the content
                // Reference -> https://msdn.microsoft.com/library/dn913749.aspx
                string data =
                    @"{""name"":""" + campaignName + @"""," + 
                    @"""type"":""only_notif""," + 
                    @"""deliveryTime"":""any"","" + 
                    @""notificationTitle"":""" + notificationTitle + 
                    @""",""notificationMessage"":""" + notificationMessage + 
                    @""",""actionUrl"":""" + actionURL + @"""}";
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("data", data),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + createURIFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                int campaignId = -1;
                if (response.StatusCode.ToString() == "OK")
                {
                    // This is the campaign id
                    campaignId = Convert.ToInt32(responseString);
                    Console.WriteLine("Campaign successfully created with id {0}", campaignId);
                }
                else
                {
                    Console.WriteLine("Campaign creation failed with error - '{0}'", responseString);
                }
                return campaignId;
            }
        }
4. После создания объявления вы увидите на портале Служб мобильного взаимодействия примерно следующее (обратите внимание что параметр State (состояние) имеет значение Draft (черновик), а Activated (статус активации) — N/A (недоступно)).
   
    ![][3]
5. `CreateAzMECampaign` создает кампанию с типом «Объявление» и возвращает ее идентификатор. `ActivateAzMECampaign` в качестве параметра для активации кампании. 
   
        static async Task<bool> ActivateAzMECampaign(int campaignId)
        {
            string activateUriFragment = "/reach/1/activate";
            using (var client = new HttpClient())
            {
                // Add Authorization Header
                client.DefaultRequestHeaders.TryAddWithoutValidation("Authorization", CreateAuthZHeader());
   
                var content = new FormUrlEncodedContent(new[] 
                {
                    new KeyValuePair<string, string>("kind", "announcement"),
                    new KeyValuePair<string, string>("id", campaignId.ToString()),
                });
   
                // Send the POST request
                var response = await client.PostAsync(url + activateUriFragment, content);
                var responseString = response.Content.ReadAsStringAsync().Result;
                if (response.StatusCode.ToString() == "OK")
                {
                    Console.WriteLine("Campaign successfully activated");
                    return true;
                }
                else
                {
                    Console.WriteLine("Campaign activation failed with error - '{0}'", responseString);
                    return false;
                }
            }
        }
6. После активации объявления вы увидите на портале Служб мобильного взаимодействия примерно следующее.
   
    ![][4]
7. Сразу после активации кампании все устройства, которые удовлетворяют ее критериям, начнут получать уведомления. 
8. Вы также заметите, что после создания кампании значение флага IsProcessed рассматриваемого элемента списка изменится на True.  

В этом примере создается простая кампания с типом «Объявление», в которой указываются в основном обязательные свойства. Вы можете изменить ее на портале, используя сведения [с этой страницы](https://msdn.microsoft.com/library/azure/mt683751.aspx). 

<!-- Images. -->
[1]: ./media/mobile-engagement-sample-backend-integration-sharepoint/sharepointlist.png
[2]: ./media/mobile-engagement-sample-backend-integration-sharepoint/properties.png
[3]: ./media/mobile-engagement-sample-backend-integration-sharepoint/new-announcement.png
[4]: ./media/mobile-engagement-sample-backend-integration-sharepoint/activate-announcement.png
[5]: ./media/mobile-engagement-sample-backend-integration-sharepoint/diagram.png



