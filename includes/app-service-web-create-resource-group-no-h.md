---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 26bfeed5315394b9030bff9c471dd5fbedca117f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
[!INCLUDE [resource group intro text](resource-group.md)]

В Cloud Shell создайте группу ресурсов с помощью команды [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *Западная Европа*. Чтобы просмотреть все поддерживаемые расположения для службы приложений уровня **Бесплатный**, выполните команду [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Обычно группа ресурсов и ресурсы создаются в ближайших регионах. 

По завершении команды в выходных данных JSON будут отображаться свойства группы ресурсов.