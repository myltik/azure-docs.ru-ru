---
title: Развертывание шаблонов в Azure Stack с помощью командной строки | Документация Майкрософт
description: Узнайте, как использовать кроссплатформенный интерфейс командной строки (CLI) для развертывания шаблонов в Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 761e09889a230642c42697b6bc4f96dc32fe03a0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316201"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Развертывание шаблонов в Azure Stack с помощью командной строки

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью командной строки можно развертывать шаблоны Azure Resource Manager в Пакет средств разработки Azure Stack. Используя шаблоны Azure Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной скоординированной операции.

## <a name="before-you-begin"></a>Перед началом работы
 - [Выполните установку и подключение](azure-stack-version-profiles-azurecli2.md) к Azure Stack с помощью Azure CLI.
 - Скачайте файлы *azuredeploy.json* и *azuredeploy.parameters.json* из [примера шаблона создания учетной записи хранения](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).
 
## <a name="deploy-template"></a>Развертывание шаблона
Перейдите к папке, в которую скачаны эти файлы, и выполните следующую команду для развертывания шаблона:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Эта команда развертывает шаблон в группу ресурсов **cliRG** в стандартном расположении подтверждения концепции Azure Stack.

## <a name="validate-template-deployment"></a>Проверка развертывания шаблона
Чтобы просмотреть эту группу ресурсов и учетную запись хранения, используйте следующие команды.

    azure group list

    azure storage account list




