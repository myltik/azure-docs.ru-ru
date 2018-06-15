---
title: Указание версии Node.js
description: Узнайте, как указать версию Node.js, используемую веб-сайтами и облачными службами Azure.
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: a20179c72b227deb14df442bea7b80cf31728aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23036849"
---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Указание версии Node.js в приложении Azure
При размещении приложения Node.js может потребоваться, чтобы приложение использовало конкретную версию Node.js. Существует несколько способов это сделать для приложений, размещенных в Azure.

## <a name="default-versions"></a>Версии по умолчанию
Версии Node.js, предоставляемые Azure, постоянно обновляются. Если не указано иное, будет использоваться версия по умолчанию, которая указана в переменной среды `WEBSITE_NODE_DEFAULT_VERSION` . Чтобы переопределить это значение по умолчанию, выполните действия, описанные в следующих разделах этой статьи.

> [!NOTE]
> При первом размещении приложения в облачной службе Azure (веб-роль или рабочая роль), Azure будет пытаться использовать ту же версию Node.js, что установлена в вашей среде разработки, если она совпадает с одной из версий по умолчанию, доступных в Azure.
>
>

## <a name="versioning-with-packagejson"></a>Управление версиями с использованием package.json
Можно указать требуемую версию Node.js, добавив в файл **package.json** следующий текст:

    "engines":{"node":version}

Где *версия* указывает на номер конкретной версии, которую нужно использовать. Вы можете задать более сложные условия для версии, например:

    "engines":{"node": "0.6.22 || 0.8.x"}

Поскольку 0.6.22 не является одной из версий, доступных в среде размещения, будет использоваться самая новая из доступных версий серии 0.8, то есть 0.8.4.

## <a name="versioning-websites-with-app-settings"></a>Управление версиями веб-сайтов с помощью параметров приложения
При размещении приложения на веб-сайте можно задать переменную среды **WEBSITE_NODE_DEFAULT_VERSION** с нужной версией.

## <a name="versioning-cloud-services-with-powershell"></a>Управление версиями облачных служб с использованием PowerShell
В случае размещения приложения в облачной службе и при использовании для развертывания Azure PowerShell, версию Node.js по умолчанию можно переопределить с помощью командлета **Set-AzureServiceProjectRole** . Например: 

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Обратите внимание, что в параметрах в инструкции выше учитывается регистр.  Можно проверить, выбрана ли правильная версия Node.js, выбрав свойство **engines** в **package.json** вашей роли.

Можно также использовать **Get-AzureServiceProjectRoleRuntime** для получения списка версий Node.js, доступных для приложений, размещенных в виде облачной службы.  Всегда проверяйте, есть ли версия Node.js, от которой зависит ваш проект, в этом списке.

## <a name="using-a-custom-version-with-azure-websites"></a>Использование настраиваемой версии с веб-сайтами Azure
Хотя Azure предоставляет несколько версий Node.js по умолчанию, может потребоваться версия, не доступная по умолчанию. Если приложение размещено как веб-сайт Azure, это можно сделать с помощью файла **iisnode.yml** . Ниже рассмотрен процесс использования настраиваемой версии Node.Js на веб-сайте Azure.

1. Создайте новый каталог, а затем создайте в каталоге файл **server.js** . Файл **server.js** должен содержать следующее:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    Будет показана версия Node.js, используемая на момент просмотра веб-сайта.
2. Создайте новый веб-сайт и запишите его имя. Например, ниже представлен пример использования [средств командной строки Azure] для создания нового веб-сайта Azure с именем **mywebsite**и последующего включения репозитория Git для веб-сайта.

        azure site create mywebsite --git
3. Создайте каталог под именем **bin**, дочерний по отношению к каталогу, содержащему файл **server.js**.
4. Загрузите конкретную версию **node.exe** (версию для Windows), которую нужно использовать с вашим приложением. Например, в следующем примере используется **curl** для загрузки версии 0.8.1:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    Сохраните файл **node.exe** в ранее созданный каталог **bin**.
5. Создайте файл **iisnode.yml** в том же каталоге, что и файл **server.js**, а затем добавьте в файл **iisnode.yml** следующее содержимое:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    Этот путь указывает расположение файла **node.exe** в проекте после публикации приложения на веб-сайте Azure.
6. Опубликуйте приложение. Например, поскольку ранее был создан веб-сайт с помощью параметра --git, следующие команды добавят файлы приложения в локальный репозиторий Git и передадут их в репозиторий веб-сайта:

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    После публикации приложения откройте веб-сайт в браузере. Должно появиться сообщение "Hello from Azure running node version: v0.8.1".

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы научились устанавливать версии Node.js, используемые приложением, узнайте, как [работа с модулями], [создавать и развертывать веб-сайт Node.js](app-service/app-service-web-get-started-nodejs.md) и [Средства командной строки Azure для Mac и Linux].

Дополнительную информацию см. в [центре разработчиков Node.js](https://azure.microsoft.com/develop/nodejs/).

[Средства командной строки Azure для Mac и Linux]:cli-install-nodejs.md
[средств командной строки Azure]:cli-install-nodejs.md
[работа с модулями]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: app-service/app-service-web-get-started-nodejs.md
