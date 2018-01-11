---
title: "Развернуть приложение в службе приложений Azure с ZIP-файл | Документы Microsoft"
description: "Дополнительные сведения о развертывания приложения в службе приложений Azure с ZIP-файл."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Развернуть приложение в службе приложений Azure с ZIP-файл

В этой статье показано, как использовать ZIP-файл для развертывания веб-приложения для [службе приложений Azure](app-service-web-overview.md). 

Это развертывание файла ZIP использует одну и ту же службу Kudu, лежит в основе непрерывной интеграции развертывания на основе. Kudu поддерживает следующие функциональные возможности для развертывания файла ZIP. 

- Удаление файлов, оставшихся после предыдущего развертывания.
- Параметр для включения в процесс построения по умолчанию, который включает в себя восстановление пакета.
- [Настройка развертывания](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), включая запуск скриптов развертывания.  
- Журналы развертывания. 

Дополнительные сведения см. в разделе [Kudu документации](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="prerequisites"></a>Технические условия

Для выполнения шагов в этой статье:

* [Создайте приложение службы приложений](/azure/app-service/) или используйте приложение, созданное для работы с другим руководством.

## <a name="create-a-project-zip-file"></a>Создать ZIP-файл проекта

>[!NOTE]
> Если вы загрузили файлов в ZIP-файле, сначала Извлеките файлы. Например, если ZIP-файл, загруженный из GitHub, невозможно развернуть этот файл как-является. GitHub добавляет дополнительные вложенные каталоги, которые не работают со службой приложения. 
>

В локальном окне терминала перейдите в корневой каталог проекта приложения. 

Этот каталог должен содержать запись файла для веб-приложения, такие как _index.html_, _index.php_, и _в файле app.js_. Он также может содержать файлы пакета управления, как _project.json_, _composer.json_, _package.json_, _bower.json_и _requirements.txt_.

Создайте ZIP-архив всего содержимого в проекте. Следующая команда использует инструмент по умолчанию в окне терминала:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Отправка ZIP-файл оболочку облака

Если вместо этого запустите Azure CLI из локального терминала, пропустите этот шаг.

Выполните здесь для загрузки ZIP-файл в оболочку облака. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Дополнительные сведения см. в разделе [сохранять файлы в оболочке облако Azure](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Развертывание ZIP-файла

Развертывание загруженный ZIP-файл на веб-приложения с помощью [az исходного развертывания веб-приложения config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) команды. Если вы решили не использовать оболочку облака, убедитесь, что Azure CLI версии 2.0.21 или более поздней версии. Чтобы просмотреть текущую версию запустите `az --version` команду в окне локальных терминала. 

Следующий пример проводит загруженный ZIP-файл. При использовании локальной установки Azure CLI, укажите путь к локальной ZIP-файл для `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Эта команда развертывает файлы и каталоги из ZIP-ФАЙЛ файлов в папку приложения службы приложений по умолчанию (`\home\site\wwwroot`) и перезапускает приложение. Если настроен любые дополнительные пользовательского процесса построения, при запуске также. Дополнительные сведения см. в разделе [Kudu документации](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Для просмотра списка развертываний. для этого приложения, необходимо использовать API REST (см. следующий раздел). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Дальнейшие действия

Чтобы изучить более сложные сценарии развертывания, ознакомьтесь с [развертыванием в Azure с помощью Git](app-service-deploy-local-git.md). Развертывание в Azure на основе Git обеспечивает систему управления версиями, восстановление пакета, MSBuild и многое другое.

## <a name="more-resources"></a>Дополнительные ресурсы

* [Kudu: Развертывание из ZIP-файла](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Учетные данные развертывания службы приложений Azure](app-service-deploy-ftp.md)
