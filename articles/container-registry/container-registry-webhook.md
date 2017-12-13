---
title: "Веб-перехватчики реестра контейнеров Azure"
description: "Узнайте, как использовать веб-перехватчики для активации событий при выполнении определенных действий в репозиториях реестров."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: nepeters
ms.openlocfilehash: 133e36179a500dc65c3a543266a7afcf9988b87d
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2017
---
# <a name="using-azure-container-registry-webhooks"></a>Использование веб-перехватчиков реестра контейнеров Azure

В реестре контейнеров Azure хранятся частные образы контейнеров Docker, а также осуществляется управление ими (подобно тому, как в Docker Hub хранятся общедоступные образы Docker). Веб-перехватчики используются для активации событий при выполнении определенных действий в одном из репозиториев реестров. Веб-перехватчики могут реагировать на события на уровне реестра. Также их можно ориентировать на определенный тег репозитория.

Дополнительные сведения о запросах веб-перехватчика см. в [справочнике по схеме веб-перехватчика реестра контейнеров Azure](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Предварительные требования

* Реестр контейнеров Azure. Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md).
* Интерфейс командной строки Docker (Docker CLI). Установите [подсистему Docker](https://docs.docker.com/engine/installation/), чтобы настроить локальный компьютер в качестве узла Docker и получить доступ к командам Docker CLI.

## <a name="create-webhook-azure-portal"></a>Создание веб-перехватчика на портале Azure

1. Войдите на [портал Azure](https://portal.azure.com)
1. Перейдите в реестр контейнеров, в котором нужно создать веб-перехватчик.
1. В разделе **Службы** выберите **Веб-перехватчики**.
1. Щелкните **Добавить** на панели инструментов веб-перехватчика.
1. Заполните форму *Создать веб-перехватчик* следующими данными:

| Значение | Описание |
|---|---|
| Имя | Имя, назначаемое веб-перехватчику. Оно может содержать только строчные буквы и цифры (всего 5–50 символов). |
| URI службы | Универсальный код ресурса (URI) для отправки веб-перехватчиком уведомлений POST. |
| Настраиваемые заголовки | Заголовки, которые требуется передавать вместе с запросом POST. Они должны быть в формате "ключ: значение". |
| "Trigger actions" (Активирующие действия) | Действия, которые активируют веб-перехватчик. Сейчас веб-перехватчики могут быть активированы действиями отправки и (или) удаления образа. |
| Состояние | Состояние веб-перехватчика после его создания. По умолчанию он включен. |
| Область | Область действия веб-перехватчика. По умолчанию это все события в реестре. Можно также указать определенный репозиторий или тег, используя формат "репозиторий:тег". |

Пример формы для веб-перехватчика приведен ниже.

![Пользовательский интерфейс создания веб-перехватчика ACR на портале Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Создание веб-перехватчика с помощью Azure CLI

Чтобы создать веб-перехватчик с помощью Azure CLI, используйте команду [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Проверка веб-перехватчика

### <a name="azure-portal"></a>Портал Azure

Прежде чем использовать веб-перехватчик для действий отправки или удаления образа контейнера, его можно проверить с помощью кнопки **Проверка связи**. После нажатия этой кнопки к указанной конечной точке отправляется общий запрос POST и ответ записывается в журнал. Используя функцию проверки связи, мы сможем проверить, правильно ли настроен веб-перехватчик.

1. Выберите веб-перехватчик, который требуется проверить.
2. В верхней части панели инструментов выберите **Проверка связи**.
3. Просмотрите ответ от конечной точки в столбце **СОСТОЯНИЕ HTTP**.

![Пользовательский интерфейс создания веб-перехватчика ACR на портале Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы протестировать веб-перехватчик ACR с помощью Azure CLI, используйте команду [az acr webhook ping](/cli/azure/acr/webhook#ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Чтобы просмотреть результаты, используйте команду [az acr webhook list-events](/cli/azure/acr/webhook#list-events).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Удаление веб-перехватчика

### <a name="azure-portal"></a>Портал Azure

Любой веб-перехватчик можно удалить, выбрав его и нажав кнопку **Удалить** на портале Azure.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Дальнейшие действия

[Azure Container Registry webhook schema reference](container-registry-webhook-reference.md) (Справочник по схеме реестра контейнеров Azure)