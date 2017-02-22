---
title: "Создание реестра контейнеров Azure с помощью Azure CLI | Документация Майкрософт"
description: "Приступите к созданию реестров контейнеров и управлению ими с помощью предварительной версии Azure CLI 2.0"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: f299cff22d00a1c765a32838647818d18f3df85d
ms.openlocfilehash: bd2f3f5331eb83f09f5d187699a39c74be6282d5

---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Создание реестра контейнеров с помощью Azure CLI
Команды в [предварительной версии Azure CLI 2.0](https://github.com/Azure/azure-cli) позволяют создавать реестр контейнеров и управлять его параметрами на компьютере Linux, Mac или Windows. Кроме того, эти действия можно выполнять на [портале Azure](container-registry-get-started-portal.md) или программным образом с помощью [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) реестра контейнеров.


* Общие сведения и основные понятия см. в статье о [реестре контейнеров Azure](container-registry-intro.md).
* Чтобы получить справку по командам интерфейса командной строки для реестра контейнеров (команды `az acr`), добавьте в любую команду параметр `-h`.

> [!NOTE]
> В настоящее время доступна предварительная версия реестра контейнеров.
> 
> 

## <a name="prerequisites"></a>Предварительные требования
* **Предварительная версия Azure CLI 2.0.** Инструкции по установке предварительной версии Azure CLI 2.0 и началу работы с ней см. [здесь](https://github.com/Azure/azure-cli/blob/master/README.rst). Войдите в свою подписку Azure, выполнив команду `az login`.
* **Группа ресурсов.** Перед созданием реестра контейнеров создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups) или используйте имеющуюся. Группа ресурсов должна находиться в расположении, где [доступна](https://azure.microsoft.com/regions/services/) служба реестра контейнеров. Примеры кода для создания группы ресурсов с помощью предварительной версии интерфейса командной строки 2.0 см. [здесь](https://github.com/Azure/azure-cli-samples/tree/master/arm). 
* **Учетная запись хранения (необязательно).** Создайте стандартную [учетную запись хранения](../storage/storage-introduction.md) Azure для хранения сведений реестра контейнеров в том же расположении. Учетную запись хранения (если она не задана при создании реестра) можно создать с помощью команды `az acr create`. Примеры кода для создания учетной записи хранения с помощью предварительной версии интерфейса командной строки 2.0 см. [здесь](https://github.com/Azure/azure-cli-samples/tree/master/storage).
* **Субъект-служба (необязательно).** Если реестр создан с помощью интерфейса командной строки, по умолчанию доступ к нему не предоставляется. В зависимости от потребностей для реестра можно назначить имеющийся субъект-службу Azure Active Directory (или создать и назначить новый) или включить учетную запись пользователя с правами администратора. См. следующие разделы этой статьи. Дополнительные сведения о доступе к реестру см. в статье [Authenticate with the container registry](container-registry-authentication.md) (Проверка подлинности с помощью реестра контейнеров). 

## <a name="create-a-container-registry"></a>Создание реестра контейнеров
Чтобы создать реестр контейнеров, выполните команду `az acr create`. 

> [!TIP]
> При создании реестра укажите глобально уникальное доменное имя верхнего уровня, содержащее только буквы и цифры. В примерах в качестве имени реестра используется имя `myRegistry`, но его нужно заменить собственным. 
> 
> 

Следующая команда создает реестр контейнеров `myRegistry` в группе ресурсов `myResourceGroup` в юго-центральном регионе США. При этом используются минимальные параметры.

```azurecli
az acr create -n myRegistry -g myResourceGroup -l southcentralus
```

* Параметр `--storage-account-name` или `-s` указывать необязательно. В этом случае учетная запись хранения создается со случайным именем в определенной группе ресурсов.

Выход аналогичен приведенному ниже:

![Выходные данные после выполнения команды az acr](./media/container-registry-get-started-azure-cli/acr_create.png)


Обратите внимание на следующее.

* `id` — идентификатор реестра в подписке, необходимый при назначении субъекта-службы. 
* `loginServer` — полное доменное имя, которое необходимо указать для [входа в реестр](container-registry-authentication.md). В рассматриваемом примере это `myregistry-contoso.exp.azurecr.io` (все знаки в нижнем регистре).

## <a name="assign-a-service-principal"></a>Назначение субъекта-службы
Команды в предварительной версии CLI 2.0 позволяют назначать субъект-службу Azure Active Directory для реестра. В этих примерах субъект-служба имеет роль владельца, но при необходимости можно назначить [другие роли](../active-directory/role-based-access-control-configure.md).

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Создание субъекта-службы и назначение доступа к реестру
Следующая команда назначает новому субъекту-службе роль владельца для доступа к идентификатору реестра, отправленному с помощью параметра `--scopes`. Укажите надежный пароль с помощью параметра `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Назначение имеющегося субъекта-службы
Если субъект-службы уже создан и ему необходимо назначить роль владельца для доступа к реестру, выполните команду, аналогичную следующей. Используйте параметр `--assignee`, чтобы передать идентификатор приложения субъекта-службы.

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Управление учетными данными администратора
Учетная запись администратора автоматически создается для каждого реестра контейнеров, но по умолчанию она отключена. В приведенных ниже примерах показаны команды `az acr` интерфейса командной строки, используемые для управления учетными данными администратора для реестра контейнеров.

### <a name="obtain-admin-user-credentials"></a>Получение учетных данных пользователя с правами администратора
```azurecli
az acr credential show -n myRegistry
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Включение пользователя с правами администратора для имеющегося реестра
```azurecli
az acr update -n myRegistry --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Отключение пользователя с правами администратора для имеющегося реестра
```azurecli
az acr update -n myRegistry --admin-enabled false
```

## <a name="list-images-and-tags"></a>Вывод списка образов и тегов
Используйте команду `az acr` интерфейса командной строки, чтобы запросить образы и теги в репозитории. 

> [!NOTE]
> В настоящее время реестр контейнеров не поддерживает команду `docker search`, используемую для запроса образов и тегов.


### <a name="list-repositories"></a>Вывод списка репозиториев
Следующий пример выводит список репозиториев реестра в формате JSON (нотация объектов JavaScript).

```azurecli
az acr repository list -n myRegistry -o json
```

### <a name="list-tags"></a>Вывод списка тегов
Следующий пример выводит список тегов репозитория **samples/nginx** в формате JSON.

```azurecli
az acr repository show-tags -n myRegistry --repository samples/nginx -o json
```

## <a name="next-steps"></a>Дальнейшие действия
* [Отправка первого образа с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md)




<!--HONumber=Jan17_HO4-->


