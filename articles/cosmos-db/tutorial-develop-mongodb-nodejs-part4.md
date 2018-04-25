---
title: Руководство по MongoDB, Angular и Node для Azure — часть 4 | Документация Майкрософт
description: Часть 4 серии руководств по созданию в Azure Cosmos DB приложения MongoDB с помощью Angular и Node и тех же API, которые используются для MongoDB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: c9f0c6f18645a8d3a5a069aa55fec0ba8e9ba547
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Создание приложения MongoDB с помощью Angular и Azure Cosmos DB. Часть 4. Создание учетной записи Azure Cosmos DB с помощью Azure CLI

Из этого руководства в нескольких частях вы узнаете, как создать приложение [API MongoDB](mongodb-introduction.md), написанное на Node.js с использованием Express, Angular и базы данных Azure Cosmos DB.

Часть 4 руководства основана на [части 3](tutorial-develop-mongodb-nodejs-part3.md). Здесь рассматриваются следующие задачи:

> [!div class="checklist"]
> * создание группы ресурсов Azure с помощью Azure CLI;
> * Создание учетной записи для базы данных Azure Cosmos DB с помощью Azure CLI

## <a name="video-walkthrough"></a>Видеоруководство

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>предварительным требованиям

Перед переходом к этой части руководства убедитесь, что выполнены все задачи из [части 3](tutorial-develop-mongodb-nodejs-part3.md). 

В этом разделе руководства можно использовать Azure Cloud Shell (в браузере) или установленный локально интерфейс командной строки [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Если вы используете Azure CLI локально, убедитесь, что у вас установлена версия не ниже Azure CLI 2.0. Выполните команду `az --version` в командной строке, чтобы проверить установленную версию. 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> В этом руководстве изложены пошаговые инструкции по созданию приложения. Готовое приложение можно скачать из [репозитория angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) на GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Создайте учетную запись Azure Cosmos DB с помощью команды [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Для `<cosmosdb-name>` используйте уникальное имя учетной записи Azure Cosmos DB. Имя должно быть уникальным среди всех учетных записей Azure Cosmos DB в Azure.
* Параметр `--kind MongoDB` разрешает Azure Cosmos DB использовать клиентские подключения MongoDB.

Выполнение команды может занять одну-две минуты. По завершении в окне терминала отобразятся сведения о новой базе данных. 

Создав учетную запись Azure Cosmos DB, сделайте следующее.
1. Откройте новое окно браузера и перейдите на портал по адресу [https://portal.azure.com](https://portal.azure.com).
1. Щелкните логотип Azure Cosmos DB ![Значок Azure Cosmos DB на портале Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) на левой панели. Отобразятся все ваши базы данных Azure Cosmos DB.
1. Выберите учетную запись Azure Cosmos DB, которую вы только что создали, перейдите на вкладку **Обзор** и прокрутите страницу вниз, чтобы просмотреть карту расположения базы данных. 

    ![Новая учетная запись Azure Cosmos DB на портале Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Прокрутите вниз в области навигации слева и щелкните вкладку **Глобальная репликация данных**. Отобразится карта с регионами, в которые можно реплицировать данные. Например, можете щелкнуть регион "Юго-восточная Австралия" или "Восточная Австралия" и реплицировать свои данные в Австралию. Дополнительные сведения о глобальной репликации см. в статье [Как работает глобальное распределение данных в Azure с помощью Cosmos DB](distribute-data-globally.md). Пока что давайте просто сохраним один экземпляр, а если потребуется репликация, мы уже будем знать, как это сделать.

    ![Новая учетная запись Azure Cosmos DB на портале Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Дополнительная информация

В этой части руководства мы выполнили следующую задачу:

> [!div class="checklist"]
> * создали группу ресурсов Azure с помощью Azure CLI;
> * создали учетную запись Azure Cosmos DB с помощью Azure CLI.

Теперь можно приступить к следующей части руководства, чтобы подключить учетную запись Azure Cosmos DB к приложению с помощью Mongoose.

> [!div class="nextstepaction"]
> [Подключение к Azure Cosmos DB с помощью Mongoose](tutorial-develop-mongodb-nodejs-part5.md)
