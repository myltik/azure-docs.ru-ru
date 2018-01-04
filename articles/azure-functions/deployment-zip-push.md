---
title: "Почтовый индекс push развертывания для функций Azure | Документы Microsoft"
description: "Используйте средства развертывания файла .zip Kudu развертывания службы для публикации функций Azure."
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
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Push ZIP-развертывания для функций Azure 
В этой статье описывается развертывание файлов проекта функция приложения Azure из ZIP-файл (сжатие). Вы узнаете, как сделать push развертывания, как с помощью Azure CLI, так и с помощью API REST. 

Функции Azure имеет полный набор параметров непрерывной интеграции и развертывания, которые предоставляются службой приложений Azure. Дополнительные сведения см. в статье [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md). 

Быстрее итерации во время разработки часто бывает проще развертывать файлы проекта приложения функция непосредственно из сжатых ZIP-файл. Это развертывание файла .zip использует одну и ту же службу Kudu, лежит в основе непрерывной интеграции развертывания на основе, включая:

+ Удаление файлов, которые были оставшихся от предыдущих развертываний.
+ Настройка развертывания, включая запуск скриптов развертывания.
+ Журналы развертывания.
+ Синхронизация функции триггеров в [плана потребления](functions-scale.md) функции приложения.

Дополнительные сведения см. в разделе [Справочник по развертыванию принудительной .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Требования к развертыванию .zip файла
ZIP-файл, использовать для развертывания принудительной должен содержать все файлы проекта в приложении функции, включая код функции. 

>[!IMPORTANT]
> При использовании .zip push развертывания из приложения функция удаляются любые файлы из существующего развертывания, которые не найдены в ZIP-файле.  

### <a name="function-app-folder-structure"></a>Функция структуру папок приложения

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Загрузите проект приложения функции

При разработке на локальном компьютере, это легко создать ZIP-файл, функция папки проекта приложения на компьютере разработчика. 

Тем не менее вы могли создать свои функции с помощью редактора на портале Azure. Чтобы загрузить свой проект приложения функции с портала: 

1. Войдите в [портал Azure](https://portal.azure.com), а затем перейдите к функции приложения.

2. На **Обзор** выберите **загрузки содержимого приложения**. Выберите параметры загрузки, а затем выберите **загрузить**.     

    ![Загрузите проект приложения функции](./media/deployment-zip-push/download-project.png)

Загруженный ZIP-файл находится в правильный формат, которые будут публиковаться в приложение функции с помощью .zip push развертывания.

ZIP-файл также можно загрузить из репозитория GitHub. Имейте в виду, что при загрузке репозитории GitHub как ZIP-файл GitHub добавляет уровень дополнительные папки для ветви. Это дополнительную папку уровня означает, что нельзя развертывать ZIP-файл напрямую, как скачать его на GitHub. Если вы используете репозитории GitHub для поддержки функций приложения, следует использовать [непрерывной интеграции](functions-continuous-deployment.md) для развертывания приложения.  

## <a name="cli"></a>Развертывание с помощью Azure CLI

Azure CLI можно использовать для запуска развертывания push. Принудительная развернуть ZIP-файл в приложении функции с помощью [развертывания источника az functionapp config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) команды. Чтобы использовать эту команду, необходимо использовать Azure CLI версии 2.0.21 или более поздней версии. Чтобы просмотреть вы используете версию Azure CLI, используйте `az --version` команды.

В следующей команде замените `<zip_file_path>` заполнитель путь к расположению ZIP-файла. Кроме того, замените `<app_name>` с уникальным именем функции приложения. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Эта команда выполняет развертывание файлов проекта из загруженного ZIP-файла для функции приложения в Azure. Затем перезапускает приложение. Чтобы просмотреть список развертываний для этой функции приложения, необходимо использовать API REST.

Если вы используете Azure CLI на локальном компьютере, `<zip_file_path>` — это путь к ZIP-файл на компьютере. Можно также запустить Azure CLI в [оболочки облако Azure](../cloud-shell/overview.md). При использовании оболочки облака, необходимо сначала передать развертывания ZIP-файла к учетной записи службы файлов Azure, связанной с вашей облачной оболочки. В этом случае `<zip_file_path>` является местом хранения, использующий учетной записи облачной оболочки. Дополнительные сведения см. в разделе [сохранять файлы в оболочке облако Azure](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Непрерывное развертывание для функций Azure](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
