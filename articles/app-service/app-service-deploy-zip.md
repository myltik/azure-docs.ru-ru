---
title: "Развертывание приложения в Службе приложений Azure с помощью ZIP-файла | Документация Майкрософт"
description: "Узнайте, как развернуть приложение в Службе приложений Azure с помощью ZIP-файла."
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
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Развертывание приложения в Службе приложений Azure с помощью ZIP-файла

В этой статье показано, как с помощью ZIP-файла развернуть веб-приложение в [Службе приложений Azure](app-service-web-overview.md). 

Развертывание из ZIP-файла осуществляется с помощью той же службы Kudu, которая обеспечивает непрерывное развертывание на основе интеграции. Kudu поддерживает следующие возможности развертывания из ZIP-файла: 

- удаление файлов, оставшихся после предыдущего развертывания;
- включение процесса сборки по умолчанию, в том числе восстановления пакетов;
- [настройку развертывания](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), в том числе выполнение скриптов развертывания;  
- журналы развертывания. 

Дополнительные сведения см. в [документации по Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы выполнить действия, описанные в этой статье, сделайте следующее:

* [Создайте приложение службы приложений](/azure/app-service/) или используйте приложение, созданное для работы с другим руководством.

## <a name="create-a-project-zip-file"></a>Создание ZIP-файла проекта

>[!NOTE]
> Если вы загрузили ZIP-файл, сначала извлеките файлы из него. Например, если вы загрузили ZIP-файл из GitHub, его нельзя развернуть "как есть". GitHub добавляет дополнительные вложенные каталоги, которые не работают со Службой приложений. 
>

В окне терминала на локальном компьютере перейдите к корневому каталогу проекта приложения. 

Этот каталог должен содержать файл записи веб-приложения, например _index.html_, _index.php_ и _app.js_. Кроме того, в нем могут содержаться файлы управления пакетами, например _project.json_, _composer.json_, _package.json_, _bower.json_ и _requirements.txt_.

Создайте ZIP-архив всего содержимого проекта. При выполнении следующей команды в окне терминала используется инструмент по умолчанию:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Отправка ZIP-файла в Cloud Shell

Если вы решили запустить Azure CLI из окна терминала на локальном компьютере, пропустите этот шаг.

Приведенные здесь действия помогут отправить ZIP-файл в Cloud Shell. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Дополнительные сведения см. в статье [Сохранение файлов в Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Развертывание ZIP-файла

Разверните загруженный ZIP-файл в веб-приложение с помощью команды [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Если вы решили не использовать Cloud Shell, убедитесь, что установлено Azure CLI версии 2.0.21 или более поздней. Чтобы просмотреть текущую версию, выполните команду `az --version` в окне терминала на локальном компьютере. 

Следующий пример развертывает загруженный ZIP-файл. При использовании локальной установки Azure CLI укажите путь к локальному ZIP-файлу `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Эта команда позволяет развертывать файлы и каталоги из ZIP-файлов в папку для приложения службы приложений по умолчанию (`\home\site\wwwroot`). Затем приложение перезапускается. Если настроен любой дополнительный пользовательский процесс сборки, он также запустится. Дополнительные сведения см. в [документации по Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Чтобы просмотреть список развертываний этого приложения, необходимо использовать интерфейсы REST API (см. следующий раздел). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Дополнительная информация

Чтобы изучить более сложные сценарии развертывания, ознакомьтесь с [развертыванием в Azure с помощью Git](app-service-deploy-local-git.md). Развертывание в Azure на основе Git обеспечивает систему управления версиями, восстановление пакета, MSBuild и многое другое.

## <a name="more-resources"></a>Дополнительные ресурсы

* [Deploying from a zip file](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) (Развертывание из ZIP-файла)
* [Учетные данные развертывания службы приложений Azure](app-service-deploy-ftp.md)
