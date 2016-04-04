<properties 
	pageTitle="Использование интерфейса API REST для доступа к интерфейсам API службы Azure Mobile Engagement" 
	description="В этой статье описывается, как использовать интерфейсы API REST Mobile Engagement для доступа к интерфейсам API службы Azure Mobile Engagement"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/10/2016" 
	ms.author="wesmc" />

#Использование REST для доступа к интерфейсам API службы Azure Mobile Engagement

Служба Azure Mobile Engagement предоставляет [интерфейс API REST для Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx), который позволяет управлять устройствами, кампаниями охвата/продвижения и т. д. В этом примере для создания кампании типа "Объявление" и последующей ее активации и отправки в набор устройств используются непосредственно интерфейсы API REST.

Если вы не хотите использовать интерфейсы API REST, мы предлагаем [файл Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json), который можно использовать со средствами создания пакетов SDK для предпочитаемого языка. Для создания пакета SDK из файла Swagger мы рекомендуем использовать средство [AutoRest](https://github.com/Azure/AutoRest). Аналогичным образом мы создали пакет SDK для .NET, который позволяет взаимодействовать с этими интерфейсами API с помощью оболочки C# без самостоятельного согласования маркера проверки подлинности и обновления. Разбор аналогичного примера в этой оболочке см. в статье [Пример интерфейса API службы в пакете SDK для .NET](mobile-engagement-dotnet-sdk-service-api.md)

В этом примере для создания и активации кампании типа "Объявление" используются непосредственно интерфейсы API REST.

## Добавление тега appInfo user\_name в приложение Mobile Engagement

В этом примере нужно добавить тег app-info в приложение Mobile Engagement. На портале мобильного охвата для приложения этот тег можно добавить, выбрав **Параметры** > **Тег (app-info)** > **Новый тег (app-info)**. Добавьте новый тег с именем **user\_name** типа **String**.

![](./media/mobile-engagement-dotnet-rest-service-api/user-name-app-info.png)



Если вы выполнили инструкции из раздела [Приступая к работе со службой Azure Mobile Engagement для универсальных приложений Windows](mobile-engagement-windows-store-dotnet-get-started.md), можно проверить отправку тега **user\_name**, просто переопределив метод `OnNavigatedTo()` в классе `MainPage` для отправки тега app-info аналогично следующему коду:

    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        base.OnNavigatedTo(e);

        String name = "Wesley"; // Prompt the user to provide this in your client app.

        Dictionary<object, object> info = new Dictionary<object, object>();
        info.Add("user_name", name);
        EngagementAgent.Instance.SendAppInfo(info);
    }
 


## Создание приложения API службы

1. Во-первых, в этом примере потребуется использовать четыре параметра проверки подлинности. Это **SubscriptionId**, **TenantId**, **ApplicationId** и **Secret**. Для получения этих параметров проверки подлинности рекомендуется использовать сценарий PowerShell, как описывается в разделе *Однократная настройка (с использованием сценария)* в учебнике [Проверка подлинности](mobile-engagement-api-authentication.md#authentication). 

2. Для демонстрации работы с интерфейсами API службы REST с целью создания и активации новой кампании типа "Объявление" мы будем использовать простое консольное приложение Windows. Откройте Visual Studio и создайте **Консольное приложение**.

3. Затем добавьте в проект пакет NuGet **Newtonsoft.Json**.

4. В файле `Program.cs` добавьте следующие инструкции `using` для следующих пространств имен:

		using System.IO;
		using System.Net;
		using Newtonsoft.Json.Linq;
		using Newtonsoft.Json;

5. Далее необходимо определить следующие константы в классе `Program`. Они будут использоваться для проверки подлинности и взаимодействия с приложением Mobile Engagement, в котором создается кампания типа "Объявление":


        // Parameters needed for authentication of API calls.
		// These are returned from the PowerShell script in the authentication tutorial. 
		// https://azure.microsoft.com/documentation/articles/mobile-engagement-api-authentication/
        static String SubscriptionId = "<Your Subscription Id>";
        static String TenantId = "<Your TenantId>";
        static String ApplicationId = "<Your Application Id>";
        static String Secret = "<Your Secret>";

		// The token for authenticating with your Mobile Engagement app.
        static String Token = null;

        // This is the Azure Resource group concept for grouping together resources 
        // See: https://azure.microsoft.com/ru-RU/documentation/articles/resource-group-portal/
        static String ResourceGroup = "MobileEngagement";

        // For Mobile Engagement operations
        // App Collection Name from the Azure portal 
        static String Collection = "<Your App Collection Name>";

        // Application Resource Name - make sure you are using the one as specified in the dashboard of the
		// Azure portal. (This is NOT the App Name)
        static String AppName = "WesmcRestTest-windows";

		// New campaign id returned from creating the new campaign and used to activate and push the campaign
		// a set of devices.
        static String NewCampaignID = null;

		// This list will hold the device Ids we choose to push the campaign to.
        static List<String> deviceList = new List<String>();


6. Добавьте в класс `Program` следующие два метода. Они будут использоваться для выполнения интерфейсов API REST и отображения ответов в консоли.

        private static async Task<HttpWebResponse> ExecuteREST(string httpMethod, string uri, string authToken, WebHeaderCollection headers = null, string body = null, string contentType = "application/json")
        {
            //=== Execute the request 
            HttpWebRequest request = (HttpWebRequest)HttpWebRequest.Create(uri);
            HttpWebResponse response = null;

            request.Method = httpMethod;
            request.ContentType = contentType;
            request.ContentLength = 0;

            if (authToken != null)
                request.Headers.Add("Authorization", authToken);

            if (headers != null)
            {
                request.Headers.Add(headers);
            }

            if (body != null)
            {
                byte[] bytes = Encoding.UTF8.GetBytes(body);

                try
                {
                    request.ContentLength = bytes.Length;
                    Stream requestStream = request.GetRequestStream();
                    requestStream.Write(bytes, 0, bytes.Length);
                    requestStream.Close();
                }
                catch (Exception e)
                {
                    Console.WriteLine(e.Message);
                }
            }

            try
            {
                response = (HttpWebResponse)await request.GetResponseAsync();
            }
            catch (WebException we)
            {
                if (we.Response != null)
                {
                    response = (HttpWebResponse)we.Response;
                }
                else
                    Console.WriteLine(we.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

            return response;
        }


        private static void DisplayResponse(dynamic data, HttpWebResponse response)
        {
            Console.WriteLine("HTTP Status " + (int)response.StatusCode + " : " + response.StatusDescription);
            Console.WriteLine(data + "\n");
        }

    }

7. Добавьте следующий код в свой метод `Main`, чтобы создать маркер проверки подлинности с полученными параметрами проверки подлинности:

        //***************************************************************************
        //*** Get a valid authorization token with your authentication parameters ***
        //***************************************************************************

        String methodUrl = "https://login.microsoftonline.com/" + TenantId + "/oauth2/token";
        String requestBody = "grant_type=client_credentials&client_id=" + ApplicationId +
                            "&client_secret=" + Secret +
                            "&resource=https://management.core.windows.net/";
        Console.WriteLine("Getting authorization token...\n");
        HttpWebResponse response = ExecuteREST("POST", methodUrl, null, null, requestBody, null).Result;
        Stream receiveStream = response.GetResponseStream();
        StreamReader readStream = new StreamReader(receiveStream, Encoding.UTF8);
        dynamic data = JObject.Parse(readStream.ReadToEnd());
        readStream.Close();
        receiveStream.Close();
        DisplayResponse(data, response);

        if (response.StatusCode == HttpStatusCode.OK)
        {
            Token = data.token_type + " " + data.access_token;
            Console.WriteLine("Got authorization token...");
            Console.WriteLine("Authorization : " + Token + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to get authorization token. Check your parameters for API calls.\n");
            return;
        }

8. Теперь, когда у нас есть действительный маркер проверки подлинности, можно создать новую рекламную кампанию с помощью интерфейса API REST [создания кампании](https://msdn.microsoft.com/library/azure/mt683742.aspx). Будет создана простая кампания типа "Объявление" с атрибутами **Всегда** и **Только уведомления** с заголовком и сообщением. Это будет кампания push-уведомлений с ручным управлением, как показано на следующем снимке экрана. Это означает, что уведомления будут отправляться только с помощью интерфейсов API.


	![](./media/mobile-engagement-dotnet-rest-service-api/manual-push.png)

	Добавьте следующий код в свой метод `Main`, чтобы создать кампанию типа "Объявление":

        //*****************************************************************************
        //*** Create a campaign to send a notification using the user-name app-info ***
        //*****************************************************************************

        String newCampaignMethodUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
               "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
               Collection + "/apps/" + AppName + "/campaigns/announcements?api-version=2014-12-01";

        String campaignRequestBody = "{ "name": "BirthdayCoupon", " +
                                        ""type": "only_notif", " +
                                        ""deliveryTime": "any", " +
                                        ""notificationType": "popup", " +
                                        ""pushMode":"manual", " +
                                        ""notificationTitle": "Happy Birthday ${user_name}", " +
                                        ""notificationMessage": "Take extra 10% off on your orders today!"}";

        Console.WriteLine("Creating new campaign...\n");
        HttpWebResponse newCampaignResponse = ExecuteREST("POST", newCampaignMethodUrl, Token, null, campaignRequestBody).Result;
        Stream receiveCampaignStream = newCampaignResponse.GetResponseStream();
        StreamReader readCampaignStream = new StreamReader(receiveCampaignStream, Encoding.UTF8);
        dynamic newCampaignData = JObject.Parse(readCampaignStream.ReadToEnd());
        readCampaignStream.Close();
        receiveCampaignStream.Close();
        DisplayResponse(newCampaignData, newCampaignResponse);

        if (newCampaignResponse.StatusCode == HttpStatusCode.Created)
        {
            NewCampaignID = newCampaignData.id;
            Console.WriteLine("Created new campaign...");
            Console.WriteLine("New Campaign Id    : " + NewCampaignID + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to create birthday campaign.\n");
            return;
        }


9. Кампанию нужно активировать, прежде чем ее можно будет передать на устройства. Идентификатор новой кампании сохраняется в переменной `NewCampaignID`. Мы будем использовать ее в качестве параметра пути URI для активации кампании с помощью интерфейса API REST [активации кампании](https://msdn.microsoft.com/library/azure/mt683745.aspx). В результате состояние кампании должно измениться на **scheduled**, несмотря на то, что она будет отправляться на устройства только вручную с помощью интерфейсов API.

	Добавьте следующий код в свой метод `Main`, чтобы активировать кампанию типа "Объявление":

        //******************************************
        //*** Activate the new birthday campaign ***
        //******************************************

        String activateCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID +
                   "/activate?api-version=2014-12-01";

        Console.WriteLine("Activating new campaign (ID : " + NewCampaignID + ")...\n");
        HttpWebResponse activateCampaignResponse = ExecuteREST("POST", activateCampaignUrl, Token).Result;
        Stream activateCampaignStream = activateCampaignResponse.GetResponseStream();
        StreamReader activateCampaignReader = new StreamReader(activateCampaignStream, Encoding.UTF8);
        dynamic activateCampaignData = JObject.Parse(activateCampaignReader.ReadToEnd());
        activateCampaignReader.Close();
        activateCampaignStream.Close();
        DisplayResponse(activateCampaignData, activateCampaignResponse);

        if (activateCampaignResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Activated new campaign...");
            Console.WriteLine("New Campaign State : " + activateCampaignData.state + "\n");
        }
        else
        {
            Console.WriteLine("*** Failed to activate birthday campaign.\n");
            return;
        }

10. Для отправки кампании необходимо предоставить идентификаторы устройств для пользователей, которые должны получать уведомления. Чтобы получить все идентификаторы устройств, мы воспользуемся интерфейсом API REST [опроса устройств](https://msdn.microsoft.com/library/azure/mt683826.aspx). Идентификатор устройства будет добавляться в список, если у него есть связанный тег appInfo **user\_name**.

	Добавьте следующий код в свой метод `Main`, чтобы получить все идентификаторы устройств и заполнить список deviceList:

        //************************************************************************
        //*** Now that the manualPush campaign is activated, get the deviceIds ***
        //*** that you want to trigger the push campaign on.                   ***
        //************************************************************************

        String getDevicesUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/devices?api-version=2014-12-01";

        Console.WriteLine("Getting device IDs...\n");
        HttpWebResponse devicesResponse = ExecuteREST("GET", getDevicesUrl, Token).Result;
        Stream devicesStream = devicesResponse.GetResponseStream();
        StreamReader devicesReader = new StreamReader(devicesStream, Encoding.UTF8);
        dynamic devicesData = JObject.Parse(devicesReader.ReadToEnd());
        devicesReader.Close();
        devicesStream.Close();
        DisplayResponse(devicesData, devicesResponse);

        if (devicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Got devices.");
            foreach (dynamic device in devicesData.value)
            {
                // Just adding all in this example
                if (device.appInfo.user_name != null)
                {
                    Console.WriteLine("Adding device for push campaign...");
                    Console.WriteLine("Device ID    : " + device.deviceId);
                    Console.WriteLine("user_name    : " + device.appInfo.user_name);
                    deviceList.Add((String)device.deviceId);
                }
            }
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine("*** Failed to get devices.\n");
			return;
        }


11. Наконец, мы отправим кампанию на все устройства с идентификаторами из списка с помощью интерфейса API REST [отправки кампании](https://msdn.microsoft.com/library/azure/mt683734.aspx). Это уведомление **в приложении**. Поэтому, чтобы пользователь мог получить уведомление, приложение должно быть запущено на устройстве.

	Добавьте следующий код в свой метод `Main`, чтобы отправить кампанию на устройства из списка deviceList:

        //**************************************************************
        //*** Trigger the manualPush campaign on the desired devices ***
        //**************************************************************

        String pushCampaignUrl = "https://management.azure.com/subscriptions/" + SubscriptionId + "/" +
                  "resourcegroups/" + ResourceGroup + "/providers/Microsoft.MobileEngagement/appcollections/" +
                   Collection + "/apps/" + AppName + "/campaigns/announcements/" + NewCampaignID + 
                   "/push?api-version=2014-12-01";

        Console.WriteLine("Triggering push for new campaign (ID : " + NewCampaignID + ")...\n");
        String deviceIds = "{"deviceIds":" + JsonConvert.SerializeObject(deviceList) + "}";
        Console.WriteLine("\n" + deviceIds + "\n");
        HttpWebResponse pushDevicesResponse = ExecuteREST("POST", pushCampaignUrl, Token, null, deviceIds).Result;
        Stream pushDevicesStream = pushDevicesResponse.GetResponseStream();
        StreamReader pushDevicesReader = new StreamReader(pushDevicesStream, Encoding.UTF8);
        dynamic pushDevicesData = JObject.Parse(pushDevicesReader.ReadToEnd());
        pushDevicesReader.Close();
        pushDevicesStream.Close();
        DisplayResponse(pushDevicesData, pushDevicesResponse);

        if (pushDevicesResponse.StatusCode == HttpStatusCode.OK)
        {
            Console.WriteLine("Triggered push on new campaign.\n");
        }
        else
        {
            Console.WriteLine("*** Failed to push campaign to the device list.\n");
        }


12. Постройте и запустите свое приложение. Вывод приложения должен быть аналогичен приведенному ниже:


		C:\Users\Wesley\AzME_Service_API_Rest\test.exe

		Getting authorization token...
		
		HTTP Status 200 : OK
		{
		  "token_type": "Bearer",
		  "expires_in": "3599",
		  "expires_on": "1458085162",
		  "not_before": "1458081262",
		  "resource": "https://management.core.windows.net/",
		  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPW
		b3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFh
		NzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0cy53
		MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoiNzJm
		F5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE3OAE
		hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-HASI8
		}
		
		Got authorization token...
		Authorization : Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNN
		aW5kb3dzLm5ldC8iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYt
		Zi1jNzE4LTQ0YzQtOGVjMS0xM2IwODExMTRmM2UiLCJhcHBpZGFjciI6IjEiLCJpZHAiOiJodHRwczovL3N0
		OGU3MTdhNGJkIiwic3ViIjoiOWIzZGQ2MDctNmYwOC00Y2Y5LTk2N2YtZmUyOGU3MTdhNGJkIiwidGlkIjoi
		iI-oF5x9gj8JJ4CjtLaH3mm1_U22Qc_AjB9mtbM97dgu3kCiUm19ISatRBoAmVz3JGW6LSv2TyCeg9TGYVrE
		vsf3hl_pY9COXicc7I501_X67GsmUgs9-EedF3STrEoY5cONTiMvwCLfeBgScgXA0ikAu62KpzMu0VFDYu-H
		
		Creating new campaign...
		
		HTTP Status 201 : Created
		{
		  "id": 24,
		  "state": "draft"
		}
		
		Created new campaign...
		New Campaign Id    : 24
		
		Activating new campaign (ID : 24)...
		
		HTTP Status 200 : OK
		{
		  "id": 24,
		  "state": "scheduled"
		}
		
		Activated new campaign...
		New Campaign State : scheduled
		
		Getting device IDs...
		
		HTTP Status 200 : OK
		{
		  "value": [
		    {
		      "meta": {
		        "lastSeen": 1458080534371,
		        "firstSeen": 1458080534371,
		        "lastLocation": 1458081389617,
		        "lastInfo": 1458080571603
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "1d6208b8f281203ecb49431e2e5ce6b3"
		    },
		    {
		      "meta": {
		        "lastSeen": 1457990584698,
		        "firstSeen": 1457949384025,
		        "lastLocation": 1457990914895,
		        "lastInfo": 1457990584597
		      },
		      "appInfo": {
		        "user_name": "Wesley"
		      },
		      "deviceId": "302486644890e26045884ee5aa0619ec"
		    }
		  ]
		}
		
		Got devices.
		Adding device for push campaign...
		Device ID    : 1d6208b8f281203ecb49431e2e5ce6b3
		user_name    : Wesley
		Adding device for push campaign...
		Device ID    : 302486644890e26045884ee5aa0619ec
		user_name    : Wesley
		
		Triggering push for new campaign (ID : 24)...
		
		
		{"deviceIds":["1d6208b8f281203ecb49431e2e5ce6b3","302486644890e26045884ee5aa0619ec"]}
		
		HTTP Status 200 : OK
		{
		  "invalidDeviceIds": []
		}
		
		Triggered push on new campaign.
		


<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0323_2016-->