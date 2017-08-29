---
title: "Развертывание веб-приложения с использованием MSDeploy, имени узла и SSL-сертификата"
description: "Использование шаблона диспетчера ресурсов Azure для развертывания веб-приложения с помощью MSDeploy и настройки пользовательского имени узла и SSL-сертификата"
services: app-service\web
manager: erikre
documentationcenter: 
author: jodehavi
ms.assetid: 66366a72-cef7-4d75-8779-f4d32ed33cf7
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: jodehavi
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a0e944d0d74ecb72a919538d54db330cbbdeef64
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Развертывание веб-приложения с использованием MSDeploy, пользовательского имени узла и SSL-сертификата
Это руководство по созданию сквозного развертывания веб-приложения Azure, использованию MSDeploy, а также по добавлению пользовательского имени узла и SSL-сертификата в шаблон ARM.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

### <a name="create-sample-application"></a>Создание примера приложения
Здесь описывается развертывание веб-приложения ASP.NET. Первым шагом является создание простого веб-приложения. (Можно также воспользоваться уже существующим приложением. В таком случае этот шаг можно пропустить.)

Откройте Visual Studio 2015 и последовательно выберите «Файл» > «Создать проект». В появившемся диалоговом окне щелкните «Интернет» > «Веб-приложение ASP.NET». В разделе «Шаблоны» щелкните «Интернет» и выберите «Шаблон MVC». Установите для параметра *Change authentication type* (Изменить тип проверки подлинности) значение *Без проверки подлинности*. Таким образом мы максимально упростим пример приложения.

Теперь у вас есть базовое веб-приложение ASP.NET, которое можно использовать при развертывании.

### <a name="create-msdeploy-package"></a>Создание пакета MSDeploy
Следующий шаг — создание пакета для развертывания веб-приложения в Azure. Чтобы сделать это, сохраните проект и запустите в командной строке следующую команду:

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

В результате в папке PackageLocation появится ZIP-пакет. Теперь приложение готово к развертыванию. Для этого лишь нужно создать шаблон диспетчера ресурсов Azure.

### <a name="create-arm-template"></a>Создание шаблона ARM
Начнем с базового шаблона ARM, который позволит создать веб-приложение и план размещения. (Обратите внимание, что для краткости параметры и переменные опущены.)

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

Далее нужно изменить ресурс веб-приложения, чтобы можно было использовать вложенный ресурс MSDeploy. Это позволит создать ссылку на созданный ранее пакет и дать диспетчеру ресурсов Azure команду использовать MSDeploy для развертывания пакета в веб-приложении Azure. Ниже показан ресурс Microsoft.Web/sites с вложенным ресурсом MSDeploy.

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Заметьте, что ресурс MSDeploy принял свойство **packageUri** , которое определяется следующим образом:

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Свойство **packageUri** принимает универсальный код ресурса (URI) учетной записи хранения, который указывает на учетную запись хранения, куда будет загружен ZIP-пакет. При развертывании шаблона диспетчер ресурсов Azure задействует [подписанный URL-адрес](../storage/common/storage-dotnet-shared-access-signature-part-1.md) , чтобы загрузить пакет из учетной записи хранения локально. Это происходит автоматически благодаря скрипту PowerShell, который загружает пакет и вызывает API управления Azure, чтобы создать необходимые ключи и передать их в шаблон в качестве параметров (*_artifactsLocation* и *_artifactsLocationSasToken*). Потребуется определить параметры папки и имени файла, куда загружается пакет, внутри контейнера хранилища.

Затем нужно добавить еще один вложенный ресурс, чтобы настроить привязки имени узла для использования личного домена. Сначала вам нужно убедиться, что у вас есть собственное имя узла, и предоставить его Azure для подтверждения. Дополнительные сведения см. в статье [Настройка личного доменного имени для службы приложений Azure](app-service-web-tutorial-custom-domain.md). Когда это будет сделано, в раздел Microsoft.Web/sites шаблона можно добавить следующее:

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Наконец, необходимо добавить еще один высокоуровневый ресурс — Microsoft.Web/certificates. Этот ресурс будет содержать SSL-сертификат и находиться на том же уровне, что и веб-приложение и план размещения.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Для настройки этого ресурса нужен действительный SSL-сертификат. Как только у вас будет такой сертификат, следует извлечь байты в формате PFX в качестве строки base64. Как один из вариантов, это можно сделать, выполнив следующую команду PowerShell:

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Полученный результат можно передать в качестве параметра в шаблон ARM для развертывания.

Теперь шаблон ARM готов.

### <a name="deploy-template"></a>Развертывание шаблона
Последний шаг — соединить все компоненты для создания сквозного развертывания. Чтобы упростить развертывание, можно воспользоваться скриптом PowerShell **Deploy-AzureResourceGroup.ps1**, который добавляется при создании проекта группы ресурсов Azure в Visual Studio. Он позволяет загрузить все артефакты, необходимые для шаблона. Для этого заранее необходимо создать требуемую учетную запись хранения. Для примера в этой статье используется общая учетная запись хранения, в которую будет загружен ZIP-пакет. Сценарий загрузит пакет в учетную запись хранения с помощью AzCopy. Как только вы передадите расположение папки с артефактами, сценарий автоматически загрузит все файлы из этого каталога в указанный контейнер хранилища. После вызова метода Deploy-AzureResourceGroup.ps1 нужно обновить привязки SSL, чтобы сопоставить пользовательское имя узла с использованием SSL-сертификата.

В следующем сценарии PowerShell показано выполнение полного развертывания с использованием вызова Deploy-AzureResourceGroup.ps1:

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

Теперь приложение развернуто, и вы можете перейти к нему по адресу https://www.yourcustomdomain.com.


