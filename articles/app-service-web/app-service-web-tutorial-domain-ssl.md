---
title: "Добавление личного домена и сертификата SSL в веб-приложение Azure | Документация Майкрософт"
description: "Узнайте, как подготовить свое веб-приложение Azure для рабочей среды, добавив фирменную символику вашей компании. Сопоставьте имя личного домена (именного домена) с вашим веб-приложением и защитите его с помощью пользовательского SSL-сертификата."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/29/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017

---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>Добавление личного домена и сертификата SSL в веб-приложение Azure

В этом руководстве показано, как быстро сопоставить имя личного домена с вашим веб-приложением Azure и защитить его с помощью пользовательского SSL-сертификата. 

## <a name="before-you-begin"></a>Перед началом работы

Перед выполнением этого примера необходимо установить [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Вам также потребуется административный доступ к странице конфигурации DNS соответствующего поставщика домена. Например, чтобы добавить `www.contoso.com`, у вас должна быть возможность настраивать записи DNS для `contoso.com`.

Наконец, вам нужен действительный PFX-файл _и_ пароль для SSL-сертификата, который будет отправлен и привязан. SSL-сертификат необходимо настроить, чтобы защитить имя личного домена. В примере выше ваш SSL-сертификат должен обеспечить защиту `www.contoso.com`. 

## <a name="step-1---create-an-azure-web-app"></a>Шаг 1. Создание веб-приложения Azure

### <a name="log-in-to-azure"></a>Вход в Azure

Теперь используем Azure CLI 2.0 в окне терминала, чтобы создать ресурсы, необходимые для размещения приложения Node.js в Azure.  Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>Создание группы ресурсов   
Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure (веб-приложений, баз данных и учетных записей хранения) и управление ими. 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

Чтобы увидеть доступные значения для `---location`, используйте команду `az appservice list-locations` Azure CLI.

## <a name="create-an-app-service-plan"></a>Создание плана службы приложений

Создайте план службы приложений, выполнив команду [az appservice plan create](/cli/azure/appservice/plan#create). 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

В следующем примере создается план службы приложений с именем `myAppServicePlan` и ценовой категорией **Базовый**.

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

После создания плана службы приложений в Azure CLI отображается информация следующего вида. 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>Создание веб-приложения

План службы приложений создан. Теперь создайте веб-приложение в рамках этого плана (`myAppServicePlan`). Веб-приложение предоставляет место для размещения и развертывания кода, а также URL-адрес для просмотра развернутого приложения. Используйте команду [az appservice web create](/cli/azure/appservice/web#create), чтобы создать веб-приложение. 

В приведенной ниже команде замените заполнитель `<app_name>` уникальным именем приложения. Это уникальное имя будет использоваться в доменном имени по умолчанию для веб-приложения, поэтому оно должно быть уникальным для всех приложений в Azure. Позже можно сопоставить любые пользовательские записи DNS с веб-приложением, прежде чем предоставлять его пользователям. 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

После создания веб-приложения в Azure CLI отображается информация следующего вида. 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

В выходных данных JSON `defaultHostName` представляет имя домена по умолчанию вашего веб-приложения. Перейдите по этому адресу в браузере.

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>Шаг 2. Настройка сопоставления DNS

На этом шаге вы добавляете сопоставление личного домена в доменное имя вашего веб-приложения по умолчанию (`<app_name>.azurewebsites.net`). Обычно этот шаг выполняется на веб-сайте вашего поставщика домена. Каждый веб-сайт регистратора доменов несколько отличается, поэтому вам следует обратиться к документации вашего поставщика. Ниже приведены некоторые общие рекомендации. 

### <a name="navigate-to-to-dns-management-page"></a>Переход на страницу управления DNS

Сначала войдите на веб-сайт вашего регистратора домена.  

Затем найдите страницу управления записями DNS. Найдите ссылки или области сайта, обозначенные как **Имя домена**, **DNS** или **Управление сервером доменных имен**. Часто ссылку можно найти, открыв сведения о своей учетной записи и найдя такую ссылку, как **Мои домены**.

Когда вы найдете эту страницу, найдите ссылку, которая позволяет добавлять или изменять записи DNS. Это может быть ссылка **Файл зоны**, **Записи DNS** или **Расширенная конфигурация**.

### <a name="create-a-cname-record"></a>Создание записи CNAME

Добавьте запись CNAME, которая сопоставляет желаемое имя поддомена с именем домена по умолчанию вашего веб-приложения (`<app_name>.azurewebsites.net`, где `<app_name>` — уникальное имя приложения).

Для примера `www.contoso.com` вы создаете CNAME, которая сопоставляет имя узла `www` с `<app_name>.azurewebsites.net`.

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>Шаг 3. Настройка личного домена в веб-приложении

Закончив настройку сопоставления имени узла на веб-сайте вашего поставщика доменов, вы можете приступать к настройке личного домена в вашем веб-приложении. Добавьте эту конфигурацию с помощью команды [az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add). 

В приведенной ниже команде замените `<app_name>` уникальным именем вашего приложения, а <your_custom_domain> полным доменным именем (например, `www.contoso.com`). 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

Теперь личный домен полностью сопоставлен с вашим веб-приложением. В своем браузере перейдите к имени личного домена. Например:

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>Шаг 4 Привязка пользовательского SSL-сертификата к веб-приложению

Теперь у вас есть веб-приложение Azure с нужным доменным именем в адресной строке браузера. Тем не менее, если вы сейчас перейдете к `https://<your_custom_domain>`, вы получите сообщение об ошибке сертификата. 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

Эта ошибка возникает потому, что веб-приложение еще не имеет привязки сертификата SSL, соответствующей имени личного домена. Тем не менее, вы не получите сообщение об ошибке, перейдя по адресу `https://<app_name>.azurewebsites.net`. Так происходит потому, что ваше приложение, как и все приложения службы приложений Azure, по умолчанию защищены SSL-сертификатом для доменных имен с использованием подстановочных знаков `*.azurewebsites.net`. 

Чтобы получить доступ к своему веб-приложению по имени личного домена, необходимо привязать SSL-сертификат для личного домена к веб-приложению, что мы и сделаем на этом шаге. 

### <a name="upload-the-ssl-certificate"></a>Отправка SSL-сертификата

Отправьте SSL-сертификат для личного домена в веб-приложение с помощью команды [az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload).

В приведенной ниже команде замените `<app_name>` уникальным именем приложения, `<path_to_ptx_file>` — путем к вашему PFX-файлу, а `<password>` — паролем вашего сертификата. 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

После отправки сертификата в Azure CLI отображается информация следующего вида.

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

В выходных данных JSON `thumbprint` представляет отпечаток отправленного сертификата. Скопируйте его значение для следующего шага.

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>Привязка отправленного SSL-сертификата к веб-приложению

Ваше веб-приложение теперь имеет имя личного домена и SSL-сертификат, обеспечивающий его безопасность. Осталось только привязать отправленный сертификат к веб-приложению. Это делается с помощью команды [az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind).

В приведенной ниже команде замените `<app_name>` уникальным именем вашего приложения, а `<thumbprint-from-previous-output>` — отпечатком сертификата, который вы получили из результатов предыдущей команды. 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

После привязки сертификата к веб-приложению в Azure CLI отображается информация следующего вида.

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

В браузере перейдите в конечную точку HTTPS имени личного домена. Например:

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>Дополнительные ресурсы

[Приобретение и настройка имени личного домена для службы приложений Azure](custom-dns-web-site-buydomains-web-app.md)
[Приобретение и настройка сертификата SSL для службы приложений Azure](web-sites-purchase-ssl-web-site.md)

