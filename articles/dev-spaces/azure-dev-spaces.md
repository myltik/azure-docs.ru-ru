---
title: Azure Dev Spaces | Документация Майкрософт
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198764"
---
# <a name="azure-dev-spaces"></a>Рабочие среды Azure для разработчиков
Служба Azure Dev Spaces помогает ускорить разработку в Kubernetes. Кроме того, благодаря Azure Dev Spaces в контейнеры Azure Kubernetes можно добавлять возможности полноценной разработки, такие как отладка, а также выполнять итеративную разработку контейнеров в облаке с помощью уже знакомых средств, таких как VS Code, Visual Studio или командная строка. Служба Azure Dev Spaces особенно актуальна при командной разработке, когда изоляция отдельных ветвей кода в соответствующих областях является критически важной частью цикла разработки.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Как служба Azure Dev Spaces упрощает разработку Kubernetes 

Такой подход обеспечивает ряд преимуществ.

* Среда разработки без использования инфраструктуры, отображающая рабочую среду, с полным доступом к облачным ресурсам.
* Отладка контейнеров Node.js и .NET Core непосредственно в Kubernetes с помощью VS Code или Visual Studio. Разработку на других языках можно вести с помощью интерфейса командной строки.
* Совместное использование экземпляра Kubernetes командой разработчиков, позволяющее сократить расходы и минимизировать настройку локальных компьютеров для новых членов команды.
* Изолированное написание кода и комплексное тестирование вместе с другими компонентами без репликации или имитации зависимостей.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)