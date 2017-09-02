---
title: "Создание частного реестра контейнеров Docker с помощью Azure CLI | Документация Майкрософт"
description: "Приступите к созданию частных реестров контейнеров Docker и управлению ими с помощью Azure CLI 2.0"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2875f4089231ed12a0312b2c2e077938440365c6
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Создание частного реестра контейнеров Docker с помощью Azure CLI 2.0
Команды в [Azure CLI 2.0](https://github.com/Azure/azure-cli) позволяют создать реестр контейнеров и управлять его параметрами на компьютере Linux, Mac или Windows. Кроме того, эти действия можно выполнять на [портале Azure](container-registry-get-started-portal.md) или программным образом с помощью [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) реестра контейнеров.


* Общие сведения и основные понятия см. в статье [Общие сведения о службе реестра контейнеров Azure](container-registry-intro.md).
* Чтобы получить справку по командам интерфейса командной строки для реестра контейнеров (команды `az acr`), добавьте в любую команду параметр `-h`.


## <a name="prerequisites"></a>Предварительные требования
* **Azure CLI 2.0.** Инструкции по установке Azure CLI 2.0 и началу работы с ним см. [здесь](/cli/azure/install-azure-cli). Войдите в свою подписку Azure, выполнив команду `az login`. Дополнительные сведения см. в статье [Get started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) (Приступая к работе с Azure CLI 2.0).
* **Группа ресурсов.** Перед созданием реестра контейнеров создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups) или используйте имеющуюся. Группа ресурсов должна находиться в расположении, где [доступна](https://azure.microsoft.com/regions/services/) служба реестра контейнеров. Справочные материалы по созданию группы ресурсов с помощью CLI 2.0 см. [здесь](/cli/azure/group).
* **Учетная запись хранения (необязательно).** Создайте стандартную [учетную запись хранения](../storage/common/storage-introduction.md) Azure для хранения сведений реестра контейнеров в том же расположении. Учетную запись хранения (если она не задана при создании реестра) можно создать с помощью команды `az acr create`. Справочные материалы по созданию учетной записи хранения с помощью CLI 2.0 см. [здесь](/cli/azure/storage/account). Хранилище класса Premium сейчас не поддерживается.
* **Субъект-служба (необязательно).** Если реестр создан с помощью интерфейса командной строки, по умолчанию доступ к нему не предоставляется. В зависимости от потребностей для реестра можно назначить имеющийся субъект-службу Azure Active Directory (или создать и назначить новый) или включить учетную запись пользователя с правами администратора. См. следующие разделы этой статьи. Дополнительные сведения о доступе к реестру см. в статье [Authenticate with the container registry](container-registry-authentication.md) (Проверка подлинности с помощью реестра контейнеров).

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
Чтобы создать реестр контейнеров, выполните команду `az acr create`.

> [!TIP]
> При создании реестра укажите глобально уникальное доменное имя верхнего уровня, содержащее только буквы и цифры. В примерах в качестве имени реестра используется имя `myRegistry1`, но его нужно заменить собственным.
>
>

Следующая команда создает реестр контейнеров `myRegistry1` в группе ресурсов `myResourceGroup`. При этом используются минимальные параметры и SKU *Базовый*.

```azurecli
az acr create --name myRegistry1 --resource-group myResourceGroup --sku Basic
```

* `--storage-account-name` является необязательным. Если это не определено, учетная запись хранения создается с именем, состоящим из имени реестра и метки времени в указанной группе ресурсов.

При создании реестра выходные данные выглядят так:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T18:36:29.124842+00:00",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry
/registries/myRegistry1",
  "location": "southcentralus",
  "loginServer": "myregistry1.azurecr.io",
  "name": "myRegistry1",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myregistry123456789"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```


Обратите внимание на следующее.

* `id` — идентификатор реестра в подписке, необходимый при назначении субъекта-службы.
* `loginServer` — полное доменное имя, которое необходимо указать для [входа в реестр](container-registry-authentication.md). В рассматриваемом примере это `myregistry1.exp.azurecr.io` (все знаки в нижнем регистре).

## <a name="assign-a-service-principal"></a>Назначение субъекта-службы
Команды в интерфейсе командной строки 2.0 позволяют назначить субъект-службу Azure Active Directory для реестра. В этих примерах субъект-служба имеет роль владельца, но при необходимости можно назначить [другие роли](../active-directory/role-based-access-control-configure.md).

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Создание субъекта-службы и назначение доступа к реестру
Следующая команда назначает новому субъекту-службе роль владельца для доступа к идентификатору реестра, отправленному с помощью параметра `--scopes`. Укажите надежный пароль с помощью параметра `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Назначение имеющегося субъекта-службы
Если субъект-службы уже создан и ему необходимо назначить роль владельца для доступа к реестру, выполните команду, аналогичную следующей. Используйте параметр `--assignee`, чтобы передать идентификатор приложения субъекта-службы.

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Управление учетными данными администратора
Учетная запись администратора автоматически создается для каждого реестра контейнеров, но по умолчанию она отключена. В приведенных ниже примерах показаны команды `az acr` интерфейса командной строки, используемые для управления учетными данными администратора для реестра контейнеров.

### <a name="obtain-admin-user-credentials"></a>Получение учетных данных пользователя с правами администратора
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Включение пользователя с правами администратора для имеющегося реестра
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Отключение пользователя с правами администратора для имеющегося реестра
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>Вывод списка образов и тегов
Используйте команду `az acr` интерфейса командной строки, чтобы запросить образы и теги в репозитории.

> [!NOTE]
> В настоящее время реестр контейнеров не поддерживает команду `docker search`, используемую для запроса образов и тегов.


### <a name="list-repositories"></a>Вывод списка репозиториев
Следующий пример выводит список репозиториев реестра в формате JSON (нотация объектов JavaScript).

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>Вывод списка тегов
Следующий пример выводит список тегов репозитория **samples/nginx** в формате JSON.

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Дальнейшие действия
* [Отправка первого образа с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md)

