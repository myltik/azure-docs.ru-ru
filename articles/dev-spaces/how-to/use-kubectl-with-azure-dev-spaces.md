---
title: Использование kubectl с Azure Dev Spaces | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 38a433a14ab977fb56a8331a057d27241f1d9783
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198747"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Использование kubectl с Azure Dev Spaces

В Azure Dev Spaces можно обращаться к кластеру Kubernetes и использовать существующие средства Kubernetes, например `kubectl`.

При выполнении команды `azds resource create` или `azds resource select` автоматически добавляется контекст конфигурации `kubectl`, поэтому средство kubectl уже должно быть подключено к кластеру Kubernetes в Azure Dev Spaces. Примеры:
- Подтверждение текущего контекста: `kubectl config current-context`.
- Вывод списка всех доступных контекстов: `kubectl config get-contexts`. 
- Изменение контекста: `kubectl config use-context <context-name>`.
- Просмотр панели мониторинга Kubernetes: выполните команду `kubectl proxy`, а затем откройте в браузере адрес, который создает эта команда (добавьте `/ui` к URL-адресу для перехода к панели мониторинга Kubernetes).
- Вывод списка запущенных служб в пространстве Azure Dev Spaces по умолчанию с именем *default*: `kubectl get services --namespace=default`.

