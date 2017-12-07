---
title: "Знакомство со службой контейнеров Azure для Kubernetes"
description: "Служба контейнеров Azure для Kubernetes упрощает развертывание и администрирование контейнерных приложений в Azure."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 62cd20474e7770c30353fc74dfc67086593fca7a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Знакомство со Службой контейнеров Azure (AKS)

Служба контейнеров Azure (AKS) позволяет упростить создание и настройку кластера виртуальных машин, настроенных для запуска контейнерных приложений, а также управление такими машинами. Это позволяет использовать имеющиеся навыки либо положиться на опыт обширного и постоянно увеличивающегося сообщества при развертывании приложений на основе контейнера в Microsoft Azure и управлении ими.

AKS позволяет пользоваться преимуществами функций корпоративного уровня в Azure, сохраняя при этом возможность переноса приложений в Kubernetes и поддержку формата образов Docker.

## <a name="managed-kubernetes-in-azure"></a>Managed Kubernetes в Azure

AKS снижает сложность управления кластером Kubernetes и связанные временные затраты, делегируя ответственность за большую их часть облаку Azure. Размещенная в Azure служба Kubernetes отвечает за критические задачи, в частности за мониторинг работоспособности и техническое обслуживание. Кроме того, вы платите только за узлы агентов в своих кластерах, а не за главные узлы. AKS как управляемая служба Kubernetes обеспечивает следующее:

> [!div class="checklist"]
> * автоматическую установку обновлений и исправлений для версий Kubernetes;
> * простое масштабирование кластеров;
> * самовосстанавливающуюся размещенную область управления (главные узлы);
> * экономию денежных средств (оплачиваются только выполняющиеся узлы пула агентов).

Так как за управление узлами в кластере AKS отвечает Azure, многие задачи больше не нужно выполнять вручную (например, обновление кластера). В связи с этим AKS не предоставляет прямой доступ (например, по протоколу SSH) к кластеру.

## <a name="using-azure-container-service-aks"></a>Использование Службы контейнеров Azure (AKS)
Задача AKS заключается в том, чтобы предоставить клиентам среду для размещения контейнеров с помощью популярных средств и технологий с открытым исходным кодом. Для этого мы предоставляем стандартные конечные точки API Kubernetes. С помощью этих стандартных конечных точек можно использовать любое программное обеспечение, способное взаимодействовать с кластером Kubernetes. Например, можно выбрать [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [Helm](https://helm.sh/) или [Draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Создание кластера Kubernetes с помощью Службы контейнеров Azure (AKS)
Чтобы начать использовать AKS, разверните кластер AKS с помощью [Azure CLI](./kubernetes-walkthrough.md) или через портал (найдите **Службу контейнеров Azure** в Marketplace). Если вы — опытный пользователь, которому требуется дополнительный контроль над шаблонами Azure Resource Manager, вы можете создать пользовательский кластер Kubernetes и развернуть его с помощью интерфейса командной строки `az`, используя проект с открытым кодом [acs-engine](https://github.com/Azure/acs-engine).

### <a name="using-kubernetes"></a>Использование Kubernetes
Kubernetes автоматизирует развертывание, масштабирование приложений-контейнеров и управление ими. Это решение предоставляет обширный набор возможностей, в том числе:
* автоматическая упаковка в контейнеры;
* самовосстановление;
* горизонтальное масштабирование;
* обнаружение служб и балансировка нагрузки;
* автоматические обновления и откаты;
* управление секретами и конфигурациями;
* оркестрация хранилища;
* пакетное выполнение.

## <a name="videos"></a>Видеоролики

Служба контейнеров Azure (AKS). "Пятница с Azure", октябрь 2017 г.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Средства разработки и развертывания приложений в Kubernetes (Azure OpenDev, июнь 2017 года):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Узнайте больше о развертывании AKS и управлении ею из краткого руководства по AKS.

> [!div class="nextstepaction"]
> [Развертывание кластера Службы контейнеров Azure (AKS)](./kubernetes-walkthrough.md)