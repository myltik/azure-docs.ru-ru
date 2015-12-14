<properties
	pageTitle="Настройка API для подключения к серверной системе в домене Azure Active Directory в PowerApps | Microsoft Azure"
	description="Настройка API для подключения защищенной в AAD серверной системы в PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Настройка API для подключения к серверным ресурсам в домене Azure Active Directory
По мере появления новых пользователей, создающих домены в Azure Active Directory (AAD), в эти домены AAD также добавляются серверные ресурсы. Можно создавать и настраивать интерфейсы API для подключения к этим серверным ресурсам.

#### Предварительные требования для начала работы

- Регистрация в службе [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Создание [среды службы приложений](powerapps-get-started-azure-portal.md).
- Установка предварительной версии [Azure PowerShell][11] 1.0 или более поздней.
- Регистрация API в своей [среде службы приложений](powerapps-register-api-hosted-in-app-service.md).

## Шаг 1. Создание приложения Active Directory и назначение ему разрешений

Для доступа к серверной системе в домене AAD создайте приложение AAD и назначьте ей соответствующие разрешения для существующей серверной части (которая также является приложением AAD). Действия

1. На [классическом портале Azure][13] перейдите в Azure Active Directory, откройте клиент (или каталог) и перейдите на вкладку **приложений**:![][14]
2. Нажмите кнопку **Добавить** внизу. Затем:  

	а) Выберите **Добавить приложение, разрабатываемое моей организацией**. б) Введите имя приложения и выберите **Веб-приложение и/или веб-API**. в) В поле **URL-адрес входа** и **URI кода приложения** введите уникальные URL-адреса в AAD и URL-адреса, подходящие для вашей организации. Например, можно указать http://powerappssignon.contoso.com или http://powerappsappid.contoso.com. Мы рекомендуем использовать URL-адрес в пределах домена AAD вашей организации. URL-адреса используются в качестве идентификаторов, их наличие необязательно. Никто не будет переходить по этим URL-адресам. Можно ввести HTTP или HTTPS.

3. На только что созданной странице приложения AAD перейдите на вкладку **Настройка**: ![][15]
4. В разделе **ключей** выберите длительность в раскрывающемся списке. Обратите внимание, что ключ появляется после выбора **Сохранить**: ![][16]
5. В поле **единого входа** добавьте ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` в качестве **URL-адреса ответа**.
6. В разделе **Разрешения для других приложений**:  

	а) Выберите **Добавить приложение**. Во всплывающем окне выберите приложение AAD, которое защищает существующую серверную часть: ![][17]

	(б) Добавьте разрешения в раскрывающемся списке: ![][18]

7. Щелкните **Сохранить** внизу.
8. Скопируйте **код клиента** и **ключ** и сохраните их. После закрытия портала Azure ключ больше не отображается. 

Дополнительные сведения о приложениях AAD см. в статье [Интеграция приложений с Azure Active Directory](../active-directory-integrating-applications.md).

## Шаг 2. Настройка API с помощью Azure PowerShell

На этом этапе портал Azure не предоставляет поддержку для инициализации конфигурации, необходимой для API. Чтобы настроить API на портале Azure, используйте следующий сценарий Azure PowerShell:

> [AZURE.TIP]Сведения об установке, настройке и запуске Azure PowerShell см. в статье [Установка и настройка Azure PowerShell][11]. Следующий сценарий работает с предварительной версией Azure PowerShell 1.0 или более поздней.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**Обратите внимание**, параметр соединения **token** очень важен. Можно выбрать собственное имя, если оно задано в виде Camel. Это имя будет использоваться позднее в серверном коде или политике API.

Теперь перейдите в [портал Azure][19] в колонку настроек **Общие** для API. Вы должны увидеть дополнительные параметры настройки: ![][21]


## Попробуйте сейчас

Откройте приложение в PowerApps. В области **Доступные подключения** отображается новый API. При выборе **Подключиться** отображается окно входа в AAD. Введите данные учетной записи AAD вашей организации. Будет создано подключение.

Теперь при выполнении вызова из приложения к API с помощью этого соединения серверная часть получает маркер пользователя AAD в HTTP-заголовке **x-ms-apim-tokens** в следующем формате [кодировки Base64][20]\:

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**Обратите внимание,** что имя свойства **token** совпадает с именем параметра подключения, используемого при настройке параметров.

После этого серверный код может получить маркер AAD из свойства **AccessToken** и при необходимости использовать его. Среда службы приложений автоматически обновляет маркер.

## Настройка политики API

При необходимости можно использовать политику API для вставки маркера AAD в стандартный заголовок **Authorization** HTTP. Таким образом, если серверному коду потребуется использовать маркер AAD, его можно будет получить стандартным способом, а не анализировать пользовательский заголовок HTTP и декодировать Base64. Для этого перейдите на портал Azure, откройте колонку **Политика** для своего API и установите следующую политику:

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

Эта политика позволяет ссылаться на значения в заголовке **x-ms-apim-tokens** в качестве декодированного объекта JObject с помощью переменной **tokens**. После этого можно использовать политику **set-header** для получения фактического маркера AAD и включения его в заголовок **Authorization**. Это та же политика, которая используется в [Azure API Management](https://azure.microsoft.com/services/api-management/). Дополнительные сведения см. в статье [Политики в Azure API Management](../api-management-howto-policies.md).

**Обратите внимание,** что имя свойства **token** совпадает с именем параметра подключения, используемого при настройке параметров.

## Сводка и дальнейшие действия

В этом разделе вы узнали, как настроить интерфейс API для подключения (и проверки подлинности) к серверным ресурсам в домене Azure Active Directory. Ниже приведены некоторые связанные разделы и ресурсы для подробного изучения PowerApps:

- [Разработка API для PowerApps](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_1203_2015-->