---
title: включение файла
description: включение файла
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e4bde4f521e0e19e7acd36260c98cfe80973e284
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198241"
---
## <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Инициализация ресурсов отладки с помощью расширения VS Code
Сначала необходимо настроить проект кода, чтобы редактор VS Code мог взаимодействовать с нашей среде разработки в Azure. Расширение VS Code для Azure Dev Spaces содержит вспомогательную команду для настройки конфигурации отладки. 

Откройте **палитру команд** (с помощью меню **Вид | Палитра команд**), включите автоматическое завершение ввода и выберите эту команду: `Azure Dev Spaces: Create configuration files for connected development`. 

В папку `.vscode` будет добавлена конфигурация отладки для Azure Dev Spaces.

![](../media/common/command-palette.png)

> [!Important]
> Прежде чем продолжить, закройте и снова откройте VS Code (это нужно делать из-за ошибки).