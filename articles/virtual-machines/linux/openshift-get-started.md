---
title: "Обзор OpenShift в Azure | Документация Майкрософт"
description: "Обзор OpenShift в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>Обзор OpenShift

OpenShift представляет собой открытую и расширяемую платформу приложений-контейнеров, которая позволяет использовать Docker и Kubernetes на предприятии.  

OpenShift включает Kubernetes для оркестрации контейнеров и управления ими. На этой платформе добавлены инструменты для разработчиков и операций, позволяющие:

- быстро разрабатывать приложения;
- легко развертывать и масштабировать;
- долгосрочно обслуживать жизненный цикл команд и приложений.

Имеется несколько предложений OpenShift, два из которых доступны для выполнения в Azure.

- Источник OpenShift
- Платформа контейнеров OpenShift
- OpenShift Online
- OpenShift Dedicated

Пользователи могут использовать 2 предложения (из 4) для самостоятельного развертывания в Azure — "Источник OpenShift" и "Платформа контейнеров OpenShift".

## <a name="openshift-origin"></a>Источник OpenShift

Это высокоуровневый проект OpenShift с [открытым кодом](https://www.openshift.org/), который поддерживается сообществом. "Источник" можно установить на CentOS или RHEL.

## <a name="openshift-container-platform"></a>Платформа контейнеров OpenShift

Версия Red Hat, готовая к внедрению на предприятии ([коммерческое предложение](https://www.openshift.com)), поддерживаемая в Red Hat. Клиент приобретает необходимые права на платформу контейнеров OpenShift и отвечает за установку всей инфраструктуры и управление ею.

Сразу после приобретения всей платформы ее можно установить в локальном центре обработки данных, общедоступном облаке (Azure, AWS, Google и т. п.) и т. д.

## <a name="openshift-online"></a>OpenShift Online

**Мультитенантная** платформа OpenShift (на базе платформы контейнера) под управлением Red Hat. Red Hat управляет всей базовой инфраструктурой (виртуальными машинами, кластером OpenShift, сетью, хранилищем и т. д.). 

Клиент развертывает контейнеры, но не может контролировать, на каких узлах запускаются контейнеры. Так как это мультитенантная среда, контейнеры могут размещаться на одних и тех же узлах виртуальной машины в качестве контейнеров других клиентов. Стоимость вычисляется из расчета на один контейнер.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

Платформа OpenShift (на базе платформы контейнера) с **одним клиентом** под управлением Red Hat. Red Hat управляет всей базовой инфраструктурой (виртуальными машинами, кластером OpenShift, сетью, хранилищем и т. д.). Кластер относится только к одному клиенту и выполняется в общедоступном облаке (AWS, Google, Azure (выпуск ожидается в начале 2018 года)). Начальный кластер включает в себя четыре узла приложений за 48 тысяч долларов в год (предварительная оплата на весь год).

## <a name="next-steps"></a>Дальнейшие действия

- [Общие предварительные требования для OpenShift в Azure](./openshift-prerequisites.md)
- [Развертывание источника OpenShift в Azure](./openshift-origin.md)
- [Развертывание платформы контейнеров OpenShift в Azure](./openshift-container-platform.md)
- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок развертывания OpenShift в Azure](./openshift-troubleshooting.md)
