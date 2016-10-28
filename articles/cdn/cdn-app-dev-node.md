<properties
	pageTitle="Приступая к работе с пакетом Azure CDN SDK для Node.js | Microsoft Azure"
	description="Узнайте, как создать приложение Node.js для управления Azure CDN."
	services="cdn"
	documentationCenter="nodejs"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="casoper"/>

# Приступая к разработке для Azure CDN

> [AZURE.SELECTOR]
- [Node.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

С помощью [пакета SDK Azure CDN для Node.js](https://www.npmjs.com/package/azure-arm-cdn) можно автоматизировать создание профилей и конечных точек CDN и управление ими. В этом руководстве описывается создание простого консольного приложения Node.js с примерами некоторых доступных операций. Это руководство не содержит подробные сведения обо всех свойствах пакета Azure CDN SDK для Node.js.

Для работы с этим учебником должен быть установлен и настроен компонент [Node.js](http://www.nodejs.org) **4.x.x** или более поздней версии. Для создания приложения Node.js вы можете использовать любой текстовый редактор. Например, в этом учебнике используется [Visual Studio Code](https://code.visualstudio.com).

> [AZURE.TIP] [Завершенный проект из этого учебника](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) доступен для скачивания на сайте MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## Создание проекта и добавление зависимостей NPM

Создав группу ресурсов для профилей CDN и назначив приложению Azure AD разрешения для управления профилями CDN и конечными точками в этой группе, мы можем приступить к созданию своего приложения.

Создайте папку для хранения приложения. С помощью консоли, в которой средства Node.js включены в текущий путь, перейдите в эту новую папку и инициализируйте проект с помощью следующей команды:
	
	npm init
	
Вам будет предложено ответить на ряд вопросов для инициализации проекта. В качестве **точки входа** в этом руководстве используется *app.js*. В приведенном ниже примере вы можете увидеть значения других параметров.

![Выходные данные NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Теперь наш проект будет инициализирован с помощью файла *packages.json*. В проекте будут использоваться некоторые библиотеки Azure, содержащиеся в пакетах NPM. Мы будем использовать клиентскую среду Azure для Node.js (ms-rest-azure) и клиентскую библиотеку Azure CDN для Node.js (azure-arm-cd). Давайте добавим эти зависимости в проект.
 
	npm install --save ms-rest-azure
	npm install --save azure-arm-cdn

Когда установка этих пакетов завершится, файл *package.json* должен выглядеть примерно так (номер версии может отличаться):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo "Error: no test specified" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

И, наконец, создайте с помощью текстового редактора пустой текстовый файл и сохраните его с именем *app.js* в корневой папке проекта. Теперь мы готовы приступить к написанию кода.

## Требования, константы, проверка подлинности и структура

Давайте откроем файл *app.js* в редакторе и создадим базовую структуру нашей программы.

1. В начале файла добавьте строки required для всех наших пакетов NPM:

	``` javascript
	var msRestAzure = require('ms-rest-azure');
	var cdnManagementClient = require('azure-arm-cdn');
	```

2. Необходимо определить несколько констант, которые будут использоваться нашими методами. Добавьте следующий код. Обязательно замените заполнители, включая **&lt;угловые скобки&gt;**, собственными значениями.

	``` javascript
	//Tenant app constants
	const clientId = "<YOUR CLIENT ID>";
	const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
	const tenantId = "<YOUR TENANT ID>";

	//Application constants
	const subscriptionId = "<YOUR SUBSCRIPTION ID>";
	const resourceGroupName = "CdnConsoleTutorial";
	const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
	```

3. Далее мы создадим клиент управления CDN и передадим ему наши учетные данные.

	``` javascript
	var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
	var cdnClient = new cdnManagementClient(credentials, subscriptionId);
	```
	
	Если вы используете аутентификацию отдельных пользователей, эти две строки будут выглядеть немного иначе.

	>[AZURE.IMPORTANT] Используйте приведенный пример кода, только если решили использовать аутентификацию отдельных пользователей, а не субъект-службу. Следите за тем, чтобы ваши учетные данные пользователя хранились в секрете.

	``` javascript
	var credentials = new msRestAzure.UserTokenCredentials(clientId, 
		tenantId, '<username>', '<password>', '<redirect URI>');
	var cdnClient = new cdnManagementClient(credentials, subscriptionId);
	```

	Не забудьте заменить правильными данными все элементы в **&lt;угловых скобках&gt;**. Вместо `<redirect URI>` укажите универсальный код ресурса (URI) перенаправления, который вы ввели при регистрации приложения в Azure AD.
	

4.  Консольное приложение Node.js будет принимать некоторые параметры командной строки. Давайте добавим проверку того, что передан хотя бы один параметр.

	```javascript
	//Collect command-line parameters
	var parms = process.argv.slice(2);

	//Do we have parameters?
	if(parms == null || parms.length == 0)
	{
		console.log("Not enough parameters!");
		console.log("Valid commands are list, delete, create, and purge.");
		process.exit(1);
	}
	```

5. Теперь мы добрались до основной части нашей программы — мы будем запускать другие функции в зависимости от того, какие параметры были переданы.

	```javascript
	switch(parms[0].toLowerCase())
	{
		case "list":
			cdnList();
			break;

		case "create":
			cdnCreate();
			break;
		
		case "delete":
			cdnDelete();
			break;

		case "purge":
			cdnPurge();
			break;

		default:
			console.log("Valid commands are list, delete, create, and purge.");
			process.exit(1);
	}
	```

6.  Также мы будем проверять, передано ли нужное число параметров; если параметры имеют неправильный формат, будут отображены подсказки. Давайте создадим для этого функции.

	```javascript
	function requireParms(parmCount) {
		if(parms.length < parmCount) {
			usageHelp(parms[0].toLowerCase());
			process.exit(1);
		}
	}

	function usageHelp(cmd) {
		console.log("Usage for " + cmd + ":");
		switch(cmd)
		{
			case "list":
				console.log("list profiles");
				console.log("list endpoints <profile name>");
				break;

			case "create":
				console.log("create profile <profile name>");
				console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
				break;
			
			case "delete":
				console.log("delete profile <profile name>");
				console.log("delete endpoint <profile name> <endpoint name>");
				break;

			case "purge":
				console.log("purge <profile name> <endpoint name> <path>");
				break;

			default:
				console.log("Invalid command.");
		}
	}
	```

7. Кроме того, мы будем использовать на клиенте управления CDN асинхронные функции, поэтому нам нужен метод для обратного вызова после завершения их работы. Давайте создадим такой метод, который будет отображать данные, полученные от клиента управления CDN (если они есть) и корректно завершать работу программы.

	```javascript
	function callback(err, result, request, response) {
		if (err) {
			console.log(err);
			process.exit(1);
		} else {
			console.log((result == null) ? "Done!" : result);
			process.exit(0);
		}
	}
	```

Теперь базовая структура программы полностью готова, и мы можем создать функции, вызываемые на основе полученных параметров.

## Вывод списка профилей CDN и конечных точек

Давайте начнем с кода, который выводит список существующих профилей и конечных точек. Я дополню код комментариями с описанием синтаксиса, чтобы было понятно, для чего предназначен каждый параметр.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## Создание профилей CDN и конечных точек

Теперь давайте напишем функцию для создания профилей и конечных точек.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## Очистка конечной точки

Одной из задач, которая может выполняться в программе после создания конечной точки, является очистка содержимого в ней.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## Удаление профилей CDN и конечных точек

Наша последняя функция будет удалять конечные точки и профили.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## Запуск программы

Теперь мы можем выполнить нашу программу Node.js в любом удобном отладчике или консоли.

> [AZURE.TIP] Если вы используете в качестве отладчика Visual Studio Code, вам потребуется настроить среду для передачи параметров командной строки. Для этой цели в Visual Studio Code используется файл **lanuch.json**. Найдите в нем свойство **args** и добавьте массив строковых значений со своими параметрами следующего вида: `"args": ["list", "profiles"]`.

Давайте начнем с получения списка профилей.

![Список профилей](./media/cdn-app-dev-node/cdn-list-profiles.png)

Мы получили пустой массив. Так и должно быть, ведь в нашей группе ресурсов пока нет профилей. Теперь давайте создадим профиль.

![Создание профиля](./media/cdn-app-dev-node/cdn-create-profile.png)

Теперь мы добавим конечную точку.

![Создать конечную точку](./media/cdn-app-dev-node/cdn-create-endpoint.png)

И в завершение давайте удалим профиль.

![Удаление профиля](./media/cdn-app-dev-node/cdn-delete-profile.png)

## Дальнейшие действия

Чтобы просмотреть описываемый в этом руководстве готовый проект, [скачайте пример](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Справочную информацию о пакете SDK Azure CDN для Node.js вы найдете [здесь](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Чтобы получить дополнительную документацию о пакете SDK Azure для Node.js, откройте [полный справочник](http://azure.github.io/azure-sdk-for-node/).

Управление ресурсами CDN с помощью [PowerShell](./cdn-manage-powershell.md).

<!---HONumber=AcomDC_0921_2016-->