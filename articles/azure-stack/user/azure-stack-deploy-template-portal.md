---
title: "Развертывание шаблонов в Azure Stack с помощью портала | Документация Майкрософт"
description: "Узнайте, как использовать портал Azure Stack для развертывания шаблонов."
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 630d86ed7e3558ae2c8a62f4e1a94f10ca11812d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Развертывание шаблонов с помощью портала Azure Stack

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

С помощью портала можно развертывать шаблоны Azure Resource Manager в пакет SDK для Azure Stack.

Используя шаблоны Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной согласованной операции.

1. Войдите на портал и нажмите кнопку **Создать**, а затем щелкните **Настраиваемый** и **Развертывание шаблона**.
2. Нажмите **Изменить шаблон**, вставьте JSON-код шаблона в колонке и нажмите кнопку **Сохранить**.
3. Щелкните **Изменить параметры**, введите значения для указанных параметров и нажмите кнопку **ОК**.
4. Щелкните **Подписка**, выберите необходимую подписку, а затем нажмите кнопку **ОК**.
5. Щелкните **Группа ресурсов**, выберите существующую группу ресурсов или создайте новую и нажмите кнопку **ОК**.
6. Щелкните **Создать**. Новый элемент на панели мониторинга отслеживает ход выполнения развертывания вашего шаблона.

## <a name="next-steps"></a>Дальнейшие действия
[Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)

