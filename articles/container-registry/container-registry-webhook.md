---
title: "Веб-перехватчики реестра контейнеров Azure | Документация Майкрософт"
description: "Веб-перехватчики реестра контейнеров Azure."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: "Docker, контейнеры, ACR"
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.openlocfilehash: 35f2b940a36ca3176cf850afd97e62af0dd797ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="using-azure-container-registry-webhooks---azure-portal"></a>Использование веб-перехватчиков реестра контейнеров Azure с помощью портала Azure

В реестре контейнеров Azure хранятся частные образы контейнеров Docker, а также осуществляется управление ими (подобно тому, как в Docker Hub хранятся общедоступные образы Docker). Веб-перехватчики используются для активации событий при выполнении определенных действий в одном из репозиториев реестров. Веб-перехватчики могут реагировать на события на уровне реестра или их можно нацелить на определенный тег репозитория. 

Общие сведения и основные понятия см. в статье [Общие сведения о частных реестрах контейнеров Docker](./container-registry-intro.md).

## <a name="prerequisites"></a>Предварительные требования 

- Управляемый реестр контейнеров Azure. Создайте управляемый реестр контейнеров в своей подписке Azure. Это можно сделать на портале Azure или с помощью Azure CLI 2.0. 
- Интерфейс командной строки Docker (Docker CLI). Установите подсистему Docker, чтобы настроить локальный компьютер в качестве узла Docker и получить доступ к командам интерфейса командной строки Docker. 

## <a name="create-webhook-azure-portal"></a>Создание веб-перехватчика на портале Azure

1. Войдите на портал Azure и перейдите к реестру, в котором нужно создать веб-перехватчики. 

2. В колонке контейнера перейдите на вкладку "Веб-перехватчики". 

3. Выберите "Добавить" на панели инструментов колонки веб-перехватчика. 

4. Заполните форму *Create Webhook* (Создание веб-перехватчика) следующими данными.

| Значение | Описание |
|---|---|
| Имя | Имя, назначаемое веб-перехватчику. Оно может содержать только 5–50 строчных букв и цифр. |
| URI службы | Универсальный код ресурса (URI) для отправки веб-перехватчиком уведомлений POST. |
| Настраиваемые заголовки | Заголовки, которые требуется передавать вместе с запросом POST. Они должны быть в формате "ключ: значение". |
| "Trigger actions" (Активирующие действия) | Действия, которые активируют веб-перехватчик. В настоящее время веб-перехватчики могут быть активированы действиями отправки и (или) удаления с образом. |
| Состояние | Состояние веб-перехватчика после его создания. По умолчанию он включен. |
| Область | Область действия веб-перехватчика. По умолчанию это все события в реестре. Можно также указать определенный репозиторий или из тег, используя формат "репозиторий: тег". |

Пример формы для веб-перехватчика приведен ниже.

![Пользовательский интерфейс DCOS](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Создание веб-перехватчика с помощью Azure CLI

Чтобы создать веб-перехватчик с помощью Azure CLI, используйте команду [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Проверка веб-перехватчика

### <a name="azure-portal"></a>Портал Azure

Прежде чем использовать веб-перехватчик для действий отправки или удаления образа контейнера, его можно проверить с помощью кнопки **Проверка связи**. Эта кнопка отправляет запрос POST к указанной конечной точке и записывает в журнал ответ. Это удобно, чтобы проверить, правильно ли настроен веб-перехватчик.

1. Выберите веб-перехватчик, который требуется проверить. 
2. В верхней части панели инструментов выберите действие "Проверка связи". 
3. Проверьте запрос и ответ.

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

Любой веб-перехватчик можно удалить, выбрав его и нажав кнопку "Удалить" на портале Azure.

### <a name="azure-cli"></a>Инфраструктура CLI Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```