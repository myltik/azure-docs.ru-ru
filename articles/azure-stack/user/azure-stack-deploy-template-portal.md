---
title: "Развертывание шаблонов в Azure Stack с помощью портала | Документация Майкрософт"
description: "Узнайте, как использовать портал Azure Stack для развертывания шаблонов."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 30c077747685ad7617148e9130926120420b098b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Развертывание шаблонов с помощью портала Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью портала можно развертывать шаблоны Azure Resource Manager в Пакет средств разработки Azure Stack.

Используя шаблоны Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной согласованной операции.

1. Войдите на портал и нажмите кнопку **Создать**, а затем щелкните **Настраиваемый** и **Развертывание шаблона**.
2. Нажмите **Изменить шаблон**, вставьте JSON-код шаблона в колонке и нажмите кнопку **Сохранить**.
3. Щелкните **Изменить параметры**, введите значения для указанных параметров и нажмите кнопку **ОК**.
4. Щелкните **Подписка**, выберите необходимую подписку, а затем нажмите кнопку **ОК**.
5. Щелкните **Группа ресурсов**, выберите существующую группу ресурсов или создайте новую и нажмите кнопку **ОК**.
6. Нажмите кнопку **Создать**. Новый элемент на панели мониторинга отслеживает ход выполнения развертывания вашего шаблона.

## <a name="next-steps"></a>Дополнительная информация
[Развертывание шаблонов с помощью PowerShell](azure-stack-deploy-template-powershell.md)

