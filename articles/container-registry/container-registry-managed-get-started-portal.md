---
title: "Создание частного реестра Docker с помощью портала Azure | Документация Майкрософт"
description: "Приступите к созданию частных реестров контейнеров Docker и управлению ими с помощью портала Azure"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Создание управляемого реестра контейнеров с помощью портала Azure

Реестр контейнеров Azure — это управляемая служба реестра контейнеров Docker, используемая для хранения частных образов контейнеров Docker. В этом руководстве рассматривается создание экземпляра управляемого реестра контейнеров Azure с помощью портала Azure.

Управляемые реестры контейнеров Azure находятся на стадии предварительной версии и не доступны во всех регионах.

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

1. Щелкните **Создать** в верхнем левом углу портала Azure.

2. Найдите в Marketplace **реестр контейнеров Azure** и выберите его.

3. Щелкните **Создать**, после чего откроется колонка создания ACR.

    ![Параметры реестра контейнеров](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. В колонке **Реестр контейнеров Azure** введите следующую информацию. Закончив, нажмите кнопку **Создать**.

    а. **Имя реестра** — глобальное уникальное доменное имя верхнего уровня для определенного реестра. В этом примере в качестве имени реестра используется имя *myAzureContainerRegistry1*, но его нужно заменить собственным. Имя может содержать только буквы и цифры.

    b. **Группа ресурсов.** Выберите имеющуюся [группу ресурсов](../azure-resource-manager/resource-group-overview.md#resource-groups) или укажите имя, чтобы создать новую.

    c. **Расположение.** Выберите расположение центра обработки данных Azure, в котором [доступна](https://azure.microsoft.com/regions/services/) служба, например **юго-центральный регион США**.

    г) **Пользователь-администратор.** При необходимости укажите пользователя с правами администратора для доступа к реестру. Этот параметр можно изменить после создания реестра.

    д. **Использование управляемого реестра.** Выберите "Да", чтобы ACR автоматически управлял хранилищем реестров, использовал веб-перехватчики и проверку подлинности AAD.

    Е. **Ценовая категория.** Выберите ценовую категорию. См. дополнительные сведения о ценах на ACR.

## <a name="log-in-to-acr-instance"></a>Вход в экземпляр ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. 

Чтобы сделать это, используйте Azure CLI 2.0. Во-первых, при необходимости войдите в Azure с помощью команды [az login](/cli/azure/#login). 

```azurecli
az login
```

Затем используйте команду [az acr login](/cli/azure/acr#login), чтобы войти в реестр контейнеров Azure.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Использование реестра контейнеров Azure

### <a name="list-container-images"></a>Список образов контейнеров

Используйте команду `az acr` интерфейса командной строки, чтобы запросить образы и теги в репозитории.

> [!NOTE]
> В настоящее время реестр контейнеров не поддерживает команду `docker search`, используемую для запроса образов и тегов.

### <a name="list-repositories"></a>Вывод списка репозиториев

Следующий пример выводит список репозиториев реестра в формате JSON (нотация объектов JavaScript).

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Вывод списка тегов

Следующий пример выводит список тегов репозитория **samples/nginx** в формате JSON.

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали управляемый экземпляр реестра контейнеров Azure с помощью портала Azure.

> [!div class="nextstepaction"]
> [Отправка первого образа с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md)
