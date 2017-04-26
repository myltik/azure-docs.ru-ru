---
title: "Создание гипермасштабируемого приложения в Azure | Документация Майкрософт"
description: "Сведения об использовании различных служб Azure для максимального повышения производительности приложения ASP.NET в Azure."
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: erikre
editor: 
ms.assetid: a4d49ac7-0f97-4997-84c5-cdb9c4465757
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 48ce80d2a25c6ad55e0520ea4cbdbc82db23f919
ms.lasthandoff: 04/04/2017


---
# <a name="build-a-hyper-scale-web-app-in-azure"></a>Создание гипермасштабируемого веб-приложения в Azure

Это руководство содержит сведения о масштабировании веб-приложения ASP.NET в Azure для увеличения количества запросов пользователей.

Прежде чем начать работу с этим руководством, убедитесь, что на вашем компьютере [установлен Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Кроме того, вам потребуется запустить пример приложения на локальном компьютере с помощью [Visual Studio](https://www.visualstudio.com/vs/).

## <a name="step-1---get-sample-application"></a>Шаг 1. Получение примера приложения
На этом шаге вы настроите локальный проект ASP.NET.

### <a name="clone-the-application-repository"></a>Клонирование репозитория приложения

Откройте терминал и `CD` в рабочий каталог. Затем клонируйте пример приложения, выполнив следующие команды: 

```powershell
git clone https://github.com/cephalin/HighScaleApp.git
```

### <a name="run-the-sample-application-in-visual-studio"></a>Запуск примера приложения в Visual Studio

Откройте решение в Visual Studio.

```powershell
cd HighScaleApp
.\HighScaleApp.sln
```

Нажмите клавишу `F5`, чтобы запустить приложение.

Этот пример веб-приложения ASP.NET создан на основе шаблона по умолчанию. Он сохраняет пользовательские сеансы и использует кэш вывода. Просмотрите `HighScaleApp\Controllers\HomeController.cs`. Метод `Index()` добавляет фрагмент данных в сеанс.

```csharp
Session.Add("visited", "true"); 
```

А методы `About()` и `Contact()` кэшируют свои выходные данные.

```csharp
[OutputCache(Duration = 60)]
```

## <a name="step-2---deploy-to-azure"></a>Шаг 2. Развертывание в Azure
На этом шаге вы создадите веб-приложение Azure и развернете в него пример приложения ASP.NET.

### <a name="create-a-resource-group"></a>Создание группы ресурсов   
Чтобы создать [группу ресурсов](../azure-resource-manager/resource-group-overview.md) в регионе "Западная Европа", выполните команду [az group create](https://docs.microsoft.com/cli/azure/group#create). В группе ресурсов будут размещаться все ресурсы Azure, которыми вы хотите управлять совместно, например веб-приложение и любая серверная часть базы данных SQL.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Чтобы увидеть доступные значения для `---location`, выполните команду [az appservice list-locations](https://docs.microsoft.com/en-us/cli/azure/appservice#list-locations).

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений
Чтобы создать [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) B1, выполните команду [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create). 

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1
```

План службы приложений — это единица масштабирования, которая может включать любое количество приложений, масштаб которых необходимо увеличить или уменьшить в пределах одной инфраструктуры службы приложений. Каждый план также имеет свою [ценовую категорию](https://azure.microsoft.com/en-us/pricing/details/app-service/). На более высоких уровнях используется лучшее оборудование и доступны дополнительные функции (например, дополнительные экземпляры масштабирования).

В этом руководстве используется уровень B1. Это минимальный уровень, который позволяет масштабировать до трех экземпляров. Вы всегда сможете изменить ценовую категорию позже, выполнив команду [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update). 

### <a name="create-a-web-app"></a>Создание веб-приложения
Чтобы создать веб-приложение с уникальным именем в `$appName`, выполните команду [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create).

```azurecli
$appName = "<replace-with-a-unique-name>"
az appservice web create --name $appName --resource-group myResourceGroup --plan myAppServicePlan
```

### <a name="set-deployment-credentials"></a>Сброс учетных данных развертывания
Чтобы настроить учетные данные развертывания на уровне учетной записи, выполните команду[az appservice web deployment user set](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/user#set).

```azurecli
az appservice web deployment user set --user-name <letters-numbers> --password <mininum-8-char-captital-lowercase-letters-numbers>
```

### <a name="configure-git-deployment"></a>Настройка развертывания Git
Чтобы настроить локальное развертывание Git, выполните команду [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git).

```azurecli
az appservice web source-control config-local-git --name $appName --resource-group myResourceGroup
```

В результате вы получите выходные данные, которые выглядят следующим образом:

```json
{
  "url": "https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git"
}
```

Настройте удаленный репозиторий Git, используя возвращенный URL-адрес. Следующая команда использует пример предыдущих выходных данных:

```powershell
git remote add azure https://user123@myuniqueappname.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-the-sample-application"></a>Развертывание примера приложения
Теперь все готово для развертывания примера приложения. Запустите `git push`.

```powershell
git push azure master
```

При появлении запроса на ввод пароля используйте пароль, указанный при выполнении `az appservice web deployment user set`.

### <a name="browse-to-azure-web-app"></a>Переход к веб-приложению Azure
Чтобы увидеть работу приложения в Azure в реальном времени, выполните команду [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse).

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-3---connect-to-redis"></a>Шаг 3. Подключение к Redis
На этом шаге вы настроите кэш Redis для Azure в качестве внешнего кэша, совместно размещенного с веб-приложением Azure. Вы можете быстро использовать Redis, чтобы кэшировать выходные данные страницы. Кроме того, при дальнейшем масштабировании веб-приложения Redis поможет надежно сохранить пользовательские сеансы в нескольких экземплярах.

### <a name="create-an-azure-redis-cache"></a>Создание кэша Redis для Azure
Чтобы создать кэш Redis для Azure и сохранить выходные данные JSON, выполните команду [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create). Укажите уникальное имя в `$cacheName`.

```powershell
$cacheName = "<replace-with-a-unique-cache-name>"
$redis = (az redis create --name $cacheName --resource-group myResourceGroup --location "West Europe" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

### <a name="configure-the-application-to-use-redis"></a>Настройка приложения для использования Redis
Отформатируйте строку подключения для кэша.

```powershell
$connstring = "$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False"
$connstring 
```

Во второй строке должны отобразиться выходные данные, аналогичные следующим:

```
mycachename.redis.cache.windows.net:6380,password=/rQP/TLz1mrEPpmh9b/gnfns/t9vBRXqXn3i1RwBjGA=,ssl=True,abortConnect=False
```

В Visual Studio создайте файл веб-конфигурации `redis.config` в корневом каталоге проекта и вставьте в него указанный ниже код. В `value` укажите строку подключения из выходных данных PowerShell.

```xml
<appSettings>
  <add key="RedisConnection" value="your-azure-redis-cache-connection-string"/>
</appSettings>
```

Если взглянуть на файл `.gitignore` в репозитории Git, вы увидите, что этот файл исключен из системы управления версиями. Таким образом ваша конфиденциальная информация находится в безопасности. 

Откройте `Web.config`. Обратите внимание на элемент `<appSettings file="redis.config">`, который получает параметры, созданные в `redis.config`. 

Найдите раздел с комментариями, содержащий сведения `<sessionState>` и `<caching>`. Раскомментируйте его.

![](./media/app-service-web-tutorial-hyper-scale-app/redisproviders.png)

Этот код ищет строку подключения Redis, определенную в `RedisConnection`. 

Теперь для управления сеансами и кэширования ваше приложение использует Redis. Нажмите клавишу `F5`, чтобы запустить приложение. При необходимости можно скачать клиент управления Redis, чтобы визуализировать данные, сохраненные в кэше.

### <a name="configure-the-connection-string-in-azure"></a>Настройка строки подключения в Azure

Для работы приложения в Azure необходимо настроить ту же строку подключения Redis в веб-приложении Azure. Так как файл `redis.config` не поддерживается в системе управления версиями, то при выполнении развертывания Git он не будет развертываться в Azure.

Выполните команду [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update), чтобы добавить строку подключения с таким же именем (`RedisConnection`).

az appservice web config appsettings update --settings "RedisConnection=$connstring" --name $appName --resource-group myResourceGroup

Помните, что `$connstring` содержит форматированную строку подключения.

### <a name="redeploy-the-application-to-azure"></a>Повторное развертывание приложения в Azure
Чтобы отправить изменения в Azure, выполните следующие команды:

```bash
git add .
git commit -m "now use Redis providers"
git push azure master
```

При появлении запроса на ввод пароля используйте пароль, указанный при выполнении `az appservice web deployment user set`.

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure
Чтобы просмотреть изменения в Azure, выполните команду [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse).

```azurecli
az appservice web browse --name $appName --resource-group myResourceGroup
```

## <a name="step-4---scale-to-multiple-instances"></a>Шаг 4. Масштабирование до нескольких экземпляров
План службы приложений — это единица масштабирования для веб-приложений Azure. Чтобы увеличить масштаб веб-приложения, необходимо добавить экземпляры в плане службы приложений.

Выполните команду [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update), чтобы масштабировать план службы приложений до трех экземпляров. Это максимальное количество, допустимое в ценовой категории B1. Помните, что B1 — это ценовая категория, выбранная во время создания плана службы приложений ранее. 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --number-of-workers 3 
```

## <a name="step-5---scale-geographically"></a>Шаг 5. Развертывание приложения в нескольких регионах
Вы можете развернуть ваше приложение в облаке Azure в нескольких регионах. Эта настройка балансирует нагрузку приложения в зависимости от географического расположения и снижает время ответа, размещая приложение рядом с клиентскими браузерами.

На этом этапе вы развернете веб-приложение ASP.NET во втором регионе с помощью [диспетчера трафика Azure](https://docs.microsoft.com/en-us/azure/traffic-manager/). По завершении этого этапа уже созданное веб-приложение будет запущено в Западной Европе, а приложение, которое мы создадим позже, — в Юго-Восточной Азии. Оба приложения будут обслуживаться с использованием одного URL-адреса диспетчера трафика.

### <a name="scale-up-the-europe-app-to-standard-tier"></a>Перенос приложения в Европе на уровень "Стандартный"
Чтобы интегрировать диспетчер трафика Azure в службе приложений, требуется ценовая категория "Стандартный". Чтобы настроить для плана службы приложений уровень S1, выполните команду [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#update). 

```azurecli
az appservice plan update --name myAppServicePlan --resource-group myResourceGroup --sku S1
```
### <a name="create-a-traffic-manager-profile"></a>Создание профиля диспетчера трафика 
Чтобы создать профиль диспетчера трафика и добавить его в группу ресурсов, выполните команду [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#create). В параметре $dnsName укажите уникальное DNS-имя.

```azurecli
$dnsName = "<replace-with-unique-dns-name>"
az network traffic-manager profile create --name myTrafficManagerProfile --resource-group myResourceGroup --routing-method Performance --unique-dns-name $dnsName
```

> [!NOTE]
> `--routing-method Performance` указывает, что профиль [направляет пользовательский трафик в ближайшую конечную точку](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="get-the-resource-id-of-the-europe-app"></a>Получение идентификатора ресурса приложения в Европе
Чтобы получить идентификатор ресурса веб-приложения, выполните команду [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show).

```azurecli
$appId = az appservice web show --name $appName --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-europe-app"></a>Добавление конечной точки диспетчера трафика для приложения в Европе
Чтобы добавить конечную точку в профиль диспетчера трафика и использовать в качестве целевого объекта идентификатор ресурса веб-приложения, выполните команду [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create).

```azurecli
az network traffic-manager endpoint create --name myWestEuropeEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appId
```

### <a name="get-the-traffic-manager-endpoint-url"></a>Получение URL-адреса конечной точки диспетчера трафика
Профиль диспетчера трафика теперь содержит конечную точку, указывающую на существующее веб-приложение. Чтобы получить URL-адрес, выполните команду [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#show). 

```azurecli
az network traffic-manager profile show --name myTrafficManagerProfile --resource-group myResourceGroup --query dnsConfig.fqdn --output tsv
```

Скопируйте выходные данные в браузер. Вы должны снова увидеть свое веб-приложение.

### <a name="create-an-azure-redis-cache-in-asia"></a>Создание кэша Redis для Azure в Азии
Теперь необходимо реплицировать веб-приложение Azure в Юго-Восточную Азию. Для начала выполните команду [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#create), чтобы создать второй кэш Redis для Azure в Юго-Восточной Азии. Этот кэш должен быть совместно размещен с вашим приложением в Азии.

```powershell
$redis = (az redis create --name $cacheName-asia --resource-group myResourceGroup --location "Southeast Asia" --sku-capacity 0 --sku-family C --sku-name Basic | ConvertFrom-Json)
```

`--name $cacheName-asia` присваивает имя кэша Западной Европы с добавлением суффикса `-asia`.

### <a name="create-an-app-service-plan-in-asia"></a>Создание плана службы приложений в Азии
Выполните команду [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create), чтобы создать второй план службы приложений в Юго-Восточной Азии, используя тот же уровень S1, что и для плана Западной Европы.

```azurecli
az appservice plan create --name myAppServicePlanAsia --resource-group myResourceGroup --location "Southeast Asia" --sku S1
```

### <a name="create-a-web-app-in-asia"></a>Создание веб-приложения в Азии
Чтобы создать второе веб-приложение, выполните команду [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#create).

```azurecli
az appservice web create --name $appName-asia --resource-group myResourceGroup --plan myAppServicePlanAsia
```

`--name $appName-asia` присваивает имя приложения Западной Европы с добавлением суффикса `-asia`.

### <a name="configure-the-connection-string-for-redis"></a>Настройка строки подключения для Redis
Чтобы добавить в веб-приложение строку подключения для кэша Юго-Восточной Азии, выполните команду [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update).

az appservice web config appsettings update --settings "RedisConnection=$($redis.hostname):$($redis.sslPort),password=$($redis.accessKeys.primaryKey),ssl=True,abortConnect=False" --name $appName-asia --resource-group myResourceGroup

### <a name="configure-git-deployment-for-the-asia-app"></a>Настройка развертывания Git для приложения в Азии
Чтобы настроить локальное развертывание Git для второго веб-приложения, выполните команду [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git).

```azurecli
az appservice web source-control config-local-git --name $appName-asia --resource-group myResourceGroup
```

В результате вы получите выходные данные, которые выглядят следующим образом:

```json
{
  "url": "https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git"
}
```

Используйте возвращенный URL-адрес, чтобы настроить второй удаленный репозиторий Git для локального репозитория. Следующая команда использует пример предыдущих выходных данных:

```bash
git remote add azure-asia https://user123@myuniqueappname-asia.scm.azurewebsites.net/myuniqueappname.git
```

### <a name="deploy-your-sample-application"></a>Развертывание примера приложения
Чтобы развернуть пример приложения во втором удаленном репозитории Git, выполните команду `git push`. 

```bash
git push azure-asia master
```

При появлении запроса на ввод пароля используйте пароль, указанный при выполнении `az appservice web deployment user set`.

### <a name="browse-to-the-asia-app"></a>Переход в приложение в Азии
Чтобы убедиться, что приложение запущено в Azure, выполните команду [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse).

```azurecli
az appservice web browse --name $appName-asia --resource-group myResourceGroup
```

### <a name="get-the-resource-id-of-the-asia-app"></a>Получение идентификатора ресурса приложения в Азии
Чтобы получить идентификатор ресурса веб-приложения в Юго-Восточной Азии, выполните команду [az appservice web show](https://docs.microsoft.com/en-us/cli/azure/appservice/web#show).

```azurecli
$appIdAsia = az appservice web show --name $appName-asia --resource-group myResourceGroup --query id --output tsv
```

### <a name="add-a-traffic-manager-endpoint-for-the-asia-app"></a>Добавление конечной точки диспетчера трафика для приложения в Азии
Чтобы добавить вторую конечную точку в профиль диспетчера трафика, выполните команду [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#create).

```azurecli
az network traffic-manager endpoint create --name myAsiaEndpoint --profile-name myTrafficManagerProfile --resource-group myResourceGroup --type azureEndpoints --target-resource-id $appIdAsia
```

### <a name="add-region-identifier-to-web-apps"></a>Добавление идентификатора региона в веб-приложения
Чтобы добавить переменные среды для конкретного региона, выполните команду [az appservice web config appsettings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update).

```azurecli
az appservice web config appsettings update --settings "Region=West Europe" --name $appName --resource-group myResourceGroup
az appservice web config appsettings update --settings "Region=Southeast Asia" --name $appName-asia --resource-group myResourceGroup
```

Код приложения уже использует этот параметр приложения. Просмотрите `HighScaleApp\Views\Home\Index.cshtml`.

### <a name="complete"></a>Готово!

Теперь попробуйте перейти по URL-адресу профиля диспетчера трафика из браузеров в разных географических регионах. В клиентских браузерах в Европе должно отображаться ASP.NET West Europe (ASP.NET, Западная Европа), а в браузерах клиентов из Азии — ASP.NET Southeast Asia (ASP.NET, Юго-Восточная Азия).

## <a name="more-resources"></a>Дополнительные ресурсы

