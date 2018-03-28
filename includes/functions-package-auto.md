---
title: включение файла
description: включение файла
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 03/12/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c306c52ff6f76e09312995974013df6840a5a1a1
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
Для локальной разработки в решении "Функции Azure" версии 2.x пакет автоматически [регистрируется как расширение привязки](../articles/azure-functions/functions-triggers-bindings.md#local-development-azure-functions-core-tools). Для разработки библиотеки классов C# в решении "Функции Azure" версий 1.x и 2.x пакет автоматически [устанавливается в проекте](../articles/azure-functions/functions-triggers-bindings.md#local-c-development-using-visual-studio-or-vs-code). 

Обратите внимание, что в исходном коде применяется нумерация версий пакета SDK WebJobs: пакет SDK WebJobs версии 2.x эквивалентен решению "Функции Azure" версии 1.x. Поэтому код решения "Функции Azure" версии 1.x находится в ветви репозитория для версий 2.x. Главная ветвь и ветвь разработки содержат код для WebJobs версии 3.x, который эквивалентен коду решения "Функции Azure" версии 2.x.
