<properties urlDisplayName="Specifying a Node.js Version" pageTitle="Указание версии Node.js" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="nodejs" title="Specifying a Node.js version in an Azure application" authors="larryfr" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="na" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />





# Установка версии Node.js в приложении Azure

При размещении приложения Node.js может потребоваться использовать в приложении конкретную версию Node.js. Для приложений, размещаемых в Azure, этого можно добиться несколькими способами.

## Версии по умолчанию

В настоящее время Azure предоставляет Node.js версий 0.6.17, 0.6.20 и 0.8.4. Если не указано иное, будет использоваться версия 0.6.20 по умолчанию.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>При первом размещении приложения в облачной службе Azure (веб-роль или рабочая роль), Azure будет пытаться использовать ту же версию Node.js, что установлена в вашей среде разработки, если она совпадает с одной из версий по умолчанию, доступных в Azure.</p>
</div>


## Управление версиями с использованием package.json

Можно указать требуемую версию Node.js, добавив в файл **package.json** следующий текст:

	"engines":{"node":version}

Где *версия* указывает на номер конкретной версии, которую нужно использовать. Можно задать более сложные условия для версии, например:

	"engines":{"node": "0.6.22 || 0.8.x"}

Поскольку 0.6.22 не является одной из версий, доступных в среде размещения, будет использоваться самая новая из доступных версий серии 0.8, то есть 0.8.4.

## Управление версиями облачных служб с использованием PowerShell

В случае размещения приложения в облачной службе и при использовании для развертывания Azure PowerShell, версию Node.js по умолчанию можно переопределить с помощью командлета **Set-AzureServiceProjectRole**. Например: 

	Set-AzureServiceProjectRole WebRole1 node 0.8.4

Можно также использовать **Get-AzureServiceProjectRoleRuntime** для получения списка версий Node.js, доступных для приложений, размещенных в виде облачной службы.

## Использование настраиваемой версии с веб-сайтами Azure

Azure предоставляет несколько версий Node.js по умолчанию, но вам может потребоваться версия, недоступная по умолчанию. Если приложение размещено как веб-сайт Azure, это можно сделать с помощью файла **iisnode.yml**. Ниже рассмотрен процесс использования настраиваемой версии Node.Js на веб-сайте Azure.

1. Создайте новый каталог, а затем создайте в каталоге файл **server.js**. Файл **server.js** должен содержать следующее:

		var http = require('http');
		http.createServer(function(req,res) {
		  res.writeHead(200, {'Content-Type': 'text/html'});
		  res.end('Hello from Azure running node version: ' + process.version + '</br>');
		}).listen(process.env.PORT || 3000);

	Будет показана версия Node.js, используемая на момент просмотра веб-сайта.

2. Создайте новый веб-сайт и запишите его имя. Например, ниже представлен пример использования [средств командной строки Azure] для создания нового веб-сайта Azure с именем **mywebsite** и последующего включения репозитория Git для веб-сайта.

		azure site create mywebsite --git

3. Создайте новый каталог с именем **bin**, дочерний по отношению к каталогу, содержащему файл **server.js**.

4. Загрузите конкретную версию **node.exe** (версию Windows), которую нужно использовать вместе с приложением. Например, в следующем примере используется **curl** для загрузки версии 0.8.1:

		curl -O http://nodejs.org/dist/v0.8.1/node.exe

	Сохраните файл **node.exe** в ранее созданный каталог **bin**.

5. Создайте файл **iisnode.yml** в том же каталоге, что и файл **server.js**, и дополните файл **iisnode.yml**следующим содержимым:

		nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

	Этот путь указывает на расположение файла **node.exe** в проекте после публикации приложения на веб-сайте Azure.

6. Опубликуйте приложение. Например, ранее был создан веб-сайт с помощью параметра --git, поэтому следующие команды добавят файлы приложения в локальный репозиторий Git и передадут их в репозиторий веб-сайта:

		git add .
		git commit -m "testing node v0.8.1"
		git push azure master

	После публикации приложения откройте веб-сайт в браузере. Должно появиться сообщение: "Hello from Azure running node version: v0.8.1".

## Дальнейшие действия

Теперь, когда вы научились указывать версии Node.js, используемые приложением, научимся [работать с модулями], [строить и развертывать веб-сайт Node.js] и [использовать средства командной строки Azure для Mac и Linux].

[Использование средств командной строки Azure для Mac и Linux]: /ru-ru/documentation/articles/xplat-cli/
[использовать средства командной строки Azure для Mac и Linux]: /ru-ru/documentation/articles/xplat-cli/
[Средства командной строки Azure]: /ru-ru/documentation/articles/xplat-cli/
[работа с модулями]: /ru-ru/documentation/articles/nodejs-use-node-modules-azure-apps/
[работать с модулями]: /ru-ru/documentation/articles/nodejs-use-node-modules-azure-apps/
[Построение и развертывание веб-сайта Node.js]: /ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[строить и развертывать веб-сайт Node.js]: /ru-ru/documentation/articles/web-sites-nodejs-develop-deploy-mac/
