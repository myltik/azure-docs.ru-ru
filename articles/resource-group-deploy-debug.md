<properties
   pageTitle="Устранение неполадок при развертывании групп ресурсов в Azure"
   description="В статье описаны распространенные проблемы, возникающие при развертывании ресурсов в Azure, и показано, как использовать портал Azure, интерфейс командной строки Azure для Mac, Linux и Windows (CLI Azure ) и PowerShell для проверки развертывания и выявления проблем."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/25/2015"
   ms.author="rasquill"/>

# Устранение неполадок при развертывании групп ресурсов в Azure

Ошибки при развертывании проще не допустить, проверив заранее несколько вещей, чем исправлять, но иногда развертывание завершается сбоем по ряду причин. В этом документе описаны средства и операции, позволяющие предотвратить простые ошибки, скачать файлы шаблонов и изучить журналы развертывания. В нем также указано, на что следует обратить внимание при проверке журналов развертывания на наличие сбоев.

## Полезные средства для работы с Azure
Модуль AzureResourceManager включает командлеты, которые помогут вам выполнять свои задачи при работе с ресурсами Azure из командной строки. Шаблоны группы ресурсов Azure представляют собой документы JSON, а API управления ресурсами Azure принимает и возвращает JSON, поэтому средства анализа JSON станут одними из первых инструментов, которые вы будете использовать для просмотра сведений о ресурсах, а также для разработки шаблонов и файлов параметров шаблонов и работы с ними.

### Средства Mac, Linux и Windows
Если вы используете интерфейс командной строки Azure для Mac, Linux или Windows, скорее всего, вы уже знакомы со стандартными средствами скачивания, такими как **[curl](http://curl.haxx.se/)**, **[wget](https://www.gnu.org/software/wget/)** или **[Resty](https://github.com/beders/Resty)**, служебными программами JSON, например **[jq](http://stedolan.github.io/jq/download/)** и **[jsawk](https://github.com/micha/jsawk)**, а также библиотеками языков, которые хорошо работают с JSON. (Многие из этих средств, например [wget](http://gnuwin32.sourceforge.net/packages/wget.htm), также портированы на Windows; на самом деле существует несколько способов сделать так, чтобы программы Linux и другие средства с открытым исходным кодом также работали в Windows.)

Этот раздел содержит некоторые команды CLI Azure, которые вы можете использовать с **jq** для эффективного получения именно тех сведений, которые вам нужны. Выберите набор инструментов, который вы привыкли использовать, чтобы лучше понять то, как используются ресурсы Azure.

### Windows PowerShell

В Windows PowerShell есть несколько основных команд для выполнения тех же процедур.

- С помощью командлета **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** скачайте такие файлы, как шаблоны группы ресурсов или JSON-файлы параметров.
- Используйте командлет **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** для преобразования строки JSON в настраиваемый объект ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)), содержащий свойство для каждого поля в строке JSON.

## Предотвращение ошибок в CLI Azure  для Mac, Linux и Windows

В CLI Azure есть несколько команд, которые помогут избежать ошибок или определить, что пошло не так.

- **azure location list**. Эта команда возвращает расположения, поддерживающие определенные типы ресурса, например поставщиков виртуальных машин. Прежде чем указывать расположение ресурса, с помощью этой команды проверьте, поддерживает ли расположение тип ресурса.

    Так как список расположений может быть длинным и включать множество поставщиков, с помощью предлагаемых средств можно изучить поставщики и расположения, прежде чем использовать расположение, которое еще не доступно. Следующий сценарий использует **jq** для поиска расположений, где доступен поставщик ресурсов для виртуальных машин Azure. ()

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**. Эта команда проверяет шаблон и его параметры перед использованием. Введите пользовательский шаблон или шаблон из коллекции и значения параметров шаблона, которые планируется использовать. Этот командлет проверяет внутреннюю непротиворечивость шаблона и соответствие набора значений параметров шаблону.

    В следующем примере показано, как проверить шаблон и все необходимые параметры (CLI Azure запрашивает значения таких параметров).

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## Использование CLI Azure для получения сведений по устранению проблем с развертыванием

- **azure group log show <resource group>**: этот командлет возвращает записи журнала для каждого развертывания группы ресурсов. Если что-то идет не так, начните с проверки журналов развертывания.

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

        Use the **--last-deployment** option to retrieve only the log for the most recent deployment. The following script uses the **--json** option and **jq** to search the log for deployment failures.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **Параметры --verbose и -vv**: используйте параметр **--verbose**, чтобы задать режим подробного протоколирования для отображения этапов операций в `stdout`. Для получения полного журнала запросов, включающего этапы, отображаемые с помощью **--verbose**, используйте параметр  **-vv**. В сообщениях часто содержатся важные сведения о причинах ошибок.

- **Ваши учетные данные Azure не настроены или просрочены**: чтобы обновить учетные данные в сеансе CLI Azure, просто введите `azure login`. Если возникают ошибки проверки подлинности, убедитесь, что вы [правильно настроили CLI Azure](xplat-cli-connect.md).

## Предотвращение ошибок в Windows PowerShell

Модуль AzureResourceManager включает командлеты, которые помогают избежать ошибок.


- **Get-AzureLocation**: этот командлет возвращает расположения, поддерживающие каждый тип ресурса. Прежде чем вводить расположение ресурса, с помощью этого командлета проверьте, поддерживает ли расположение тип ресурса.


- **Test-AzureResourceGroupTemplate**: протестируйте шаблон и его параметры перед использованием. Введите пользовательский шаблон или шаблон из коллекции и значения параметров шаблона, которые планируется использовать. Этот командлет проверяет внутреннюю непротиворечивость шаблона и соответствие набора значений параметров шаблону.

## Получение сведений для устранению проблем с развертыванием в Windows PowerShell

- **Get-AzureResourceGroupLog**: этот командлет возвращает записи журнала для каждого развертывания группы ресурсов. Если что-то идет не так, начните с проверки журналов развертывания.

- **Verbose и Debug**: командлеты в модуле AzureResourceManager обращаются к интерфейсам API REST, которые выполняют фактическую работу. Чтобы увидеть сообщения, возвращаемые интерфейсами API, присвойте переменной $DebugPreference значение "Continue" и используйте общий параметр Verbose в командах. В сообщениях часто содержатся важные сведения о причинах ошибок.

- **Ваши учетные данные Azure не настроены или просрочены**: чтобы обновить учетные данные в сеансе Windows PowerShell, используйте командлет Add-AzureAccount. Учетных данных в файле параметров публикации недостаточно для командлетов в модуле AzureResourceManager.

## Проблемы с проверкой подлинности, подписками, ролями и квотами

Успешному развертыванию могут препятствовать одна или несколько из проблем, касающихся проверки подлинности, авторизации и Azure Active Directory. Независимо от того, как вы управляете группами ресурсов Azure, удостоверение, используемое для входа в учетную запись, должно быть объектом Azure Active Directory, субъектом-службой, также называемым рабочей учетной записью, или идентификатором организации.

Но Azure Active Directory позволяет вам или вашему администратору точно управлять тем, какие удостоверения могут получать доступ к тем или иным ресурсам. Если происходит сбой развертывания, проверьте запросы на наличие проблем с проверкой подлинности или авторизацией, а также изучите журналы развертывания для группы ресурсов. Может оказаться, что хотя у вас есть разрешения для некоторых ресурсов, их нет для других. Используя CLI Azure, вы можете проверить клиенты и пользователей Azure Active Directory с помощью команд `azure ad`. (Полный список команд CLI Azure см. в статье [Использование CLI Azure для Mac, Linux и Windows со средствами управления ресурсами Azure](azure-cli-arm-commands.md).)

Кроме того, проблемы могут возникнуть, когда развертывание достигает квоты по умолчанию, которая может задаваться для группы ресурсов, подписок, учетных записей, а также других областей. Убедитесь, что у вас достаточно ресурсов для надлежащего развертывания. Полные сведения о квотах см. в статье [Подписка Azure и ограничения служб, квоты и ограничения](azure-subscription-service-limits.md).


## Проблемы режимов CLI Azure и PowerShell

Возможно, вы сталкивались с ситуацией, когда ресурсы Azure, развернутые с помощью API управления службами или классического портала, не отображаются с помощью API управления ресурсами или портала Azure. Для управления ресурсами важно использовать тот же API управления или портал, который вы применяли для их создания. Если ресурс исчез, проверьте, доступен ли он, используя другой API управления или портал.

## Проблемы с регистрацией поставщика ресурсов Azure

Ресурсами управляет поставщик ресурсов, и для учетной записи или подписки может быть включено использование конкретного поставщика. Если поставщик включен для использования, он также должен быть зарегистрирован. Большинство поставщиков регистрируются автоматически порталом Azure или интерфейсом командной строки, который вы используете, но это происходит не всегда.

Чтобы узнать, зарегистрирован ли поставщик для использования, выполните в CLI Azure команду `azure provider list` (приведен сокращенный пример вывода).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

    Again, if you want more information about providers, including their regional availability, type `azure provider list --json`. The following selects only the first one in the list to view:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


Если поставщик требует регистрации, используйте команду `azure provider register <namespace>`, где значение *namespace* берется из приведенного выше списка.

## Как узнать, что развертывание пользовательских шаблонов завершилось успешно

Если вы используете шаблоны, которые создали сами, важно понимать, что система управления ресурсами Azure сообщает об успешном выполнении при успешном возврате всех поставщиков из развертывания. Это значит, что все элементы ваших шаблонов были развернуты для использования.

Тем не менее это не обязательно означает, что ваша группа ресурсов **активна и готова для пользователей**. Например, для большинства развертываний требуется скачать обновления, дождаться других ресурсов, не являющихся шаблонами, или установить сложные сценарии либо другое исполняемое действие, о которым Azure не знает, так как оно не отслеживается поставщиком. В таких случаях ресурсы могут быть готовы к практическому использованию только спустя некоторое время. В результате этого следует ожидать, что состояние развертывания меняется на "успешно" за некоторое время до того, прежде чем развертывание можно будет использовать.

Тем не менее вы можете сделать так, чтобы Azure не сообщал об успешном развертывании, создав настраиваемый сценарий для пользовательского шаблона (например, с помощью [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)), который умеет отслеживать все развертывание на предмет готовности в рамках всей системы и возвращает успешный результат только в том случае, если пользователи могут работать со всем развертыванием. Если вы хотите сделать так, чтобы ваше расширение выполнялось последним, используйте свойство **dependsOn** в шаблоне. Пример этого можно просмотреть [здесь](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Слияние шаблонов

Иногда нужно объединить два шаблона или запустить дочерний шаблон из родительского. Для этого в главном шаблоне можно воспользоваться ресурсом развертывания, с помощью которого можно развернуть дочерний шаблон.


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## Использование нескольких групп ресурсов

Часто требуется использовать ресурс из-за пределов текущей группы ресурсов, где разворачивается шаблон. Наиболее распространенным случаем такого поведения является использование учетной записи хранения или виртуальной сети в альтернативной группе ресурсов. Это часто требуется для того, чтобы удаление группы ресурсов, которая содержит виртуальные машины, не привело к удалению больших двоичных объектов VHD или виртуальной сети, которая используется несколькими группами ресурсов. В следующем примере показано, как ресурс из внешней группы ресурсов можно легко использовать:


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }



## Дальнейшие действия

Чтобы освоить создание шаблонов, прочтите статью [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md) и найдите развертываемые примеры в [репозитории AzureRMTemplates](https://github.com/azurermtemplates/azurermtemplates). Пример свойства **dependsOn** приведен в [шаблоне подсистемы балансировки нагрузки с правилом NAT для входящего трафика](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!--HONumber=52-->
