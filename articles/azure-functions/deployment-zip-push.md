---
title: "Развертывание из ZIP-файла для Функций Azure | Документация Майкрософт"
description: "Публикация Функций Azure с помощью средств непрерывного развертывания службы развертывания Kudu из ZIP-файла."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: faddb73522200f60f18294dc43e8d235943f8bbb
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Непрерывное развертывание Функций Azure из ZIP-файла 
В этой статье описывается развертывание файлов проекта приложения-функции Azure из (сжатого) ZIP-файла. Вы узнаете, как принудительно начать развертывание, как с помощью интерфейса командной строки Azure, так и с помощью REST API. 

Служба приложений Azure предоставляет широкий спектр параметров для непрерывной интеграции и развертывания Функций Azure. Дополнительные сведения см. в статье [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md). 

Чтобы ускорить итерации во время развертывания, зачастую бывает проще развертывать файлы проекта приложения-функции непосредственно из сжатого ZIP-файла. Это развертывание ZIP-файла осуществляется с помощью той же службы Kudu, которая обеспечивает непрерывное развертывание на основе интеграции, включая:

+ удаление файлов, оставшихся после предыдущих развертываний;
+ настройку развертывания, в том числе выполнение скриптов развертывания;
+ журналы развертывания;
+ синхронизацию триггеров функций в приложении-функции [План потребления](functions-scale.md).

Дополнительные сведения см. в [справочнике по принудительному развертыванию из ZIP-файлов](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Требования к развертыванию из ZIP-файла
Используемый для принудительного развертывания ZIP-файл должен содержать все файлы проекта в приложении-функции, включая код функции. 

>[!IMPORTANT]
> При использовании принудительного развертывания из ZIP-файла любые файлы из существующего развертывания, которые не найдены в ZIP-файле, будут удалены из приложения-функции.  

### <a name="function-app-folder-structure"></a>Структура папок приложения-функции

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Загрузка проекта приложения-функции

При разработке на локальном компьютере несложно создать ZIP-файл папки проекта приложения-функции на компьютере разработчика. 

Тем не менее вы могли создать свои функции с помощью редактора на портале Azure. Чтобы загрузить проект приложения-функции с портала: 

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему приложению-функции.

2. На вкладке **Обзор** выберите **Загрузить содержимое приложения**. Выберите параметры скачивания, а затем выберите команду **Загрузить**.     

    ![Загрузка проекта приложения-функции](./media/deployment-zip-push/download-project.png)

Загруженный ZIP-файл обладает правильным форматом, и он должен быть опубликован в приложении-функции с помощью принудительного развертывания из ZIP-файла.

ZIP-файл также можно загрузить из репозитория GitHub. Имейте в виду, что при загрузке репозитория GitHub как ZIP-файла GitHub добавляет еще один уровень папки для ветви. Этот дополнительный уровень папки означает, что нельзя просто напрямую развернуть ZIP-файл после его загрузки из GitHub. Если вы используете репозиторий GitHub для поддержки приложения-функции, для развертывания своего приложения следует использовать [непрерывную интеграцию](functions-continuous-deployment.md).  

## <a name="cli"></a>Развертывание с помощью интерфейса командной строки Azure

Azure CLI можно использовать для активации принудительного развертывания. Активируйте принудительное развертывание ZIP-файла в свое приложение-функцию с помощью команды [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip). Чтобы использовать эту команду, необходимо использовать Azure CLI версии 2.0.21 или более поздней версии. Чтобы просмотреть версию Azure CLI, воспользуйтесь командой `az --version`.

В следующей команде вместо заполнителя `<zip_file_path>` укажите путь к расположению ZIP-файла. Кроме того, замените `<app_name>` уникальным именем своего приложения-функции. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Эта команда выполняет развертывание файлов проекта из загруженного ZIP-файла для приложения-функции в Azure. Затем приложение перезапускается. Чтобы просмотреть список развертываний для этого приложения-функции, необходимо использовать REST API.

Если вы используете Azure CLI на локальном компьютере, `<zip_file_path>` — это путь к ZIP-файлу на компьютере. Можно также запустить Azure CLI в [Azure Cloud Shell](../cloud-shell/overview.md). При использовании Cloud Shell необходимо сначала отправить ZIP-файл развертывания в учетную запись Файлы Azure, связанную с Cloud Shell. В этом случае `<zip_file_path>` — это место хранения, используемое в учетной записи Cloud Shell. Дополнительные сведения см. в статье [Сохранение файлов в Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
