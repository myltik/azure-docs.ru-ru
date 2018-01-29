---
title: "Оценка пакета средств разработки Azure Stack | Документация Майкрософт"
description: "Сведения о том, как развернуть пакет средств разработки Azure Stack для оценки."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 4ad2e0a91e2fd5023417722fc0a1a6fae93960d0
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Краткое руководство по оценке пакета средств разработки Azure Stack

*Область применения: комплект разработки Azure Stack*

[Пакет средств разработки Azure Stack](azure-stack-poc.md) — это среда тестирования и разработки, которую можно развернуть для оценки и демонстрации функций и служб Azure Stack. Чтобы запустить ее, необходимо подготовить оборудование среды и выполнить ряд скриптов (это займет несколько часов). После этого можно войти на портал администрирования и пользовательский портал для управления Azure Stack и тестирования предложений. 

1. [**Планирование оборудования, программного обеспечения и сети**](azure-stack-deploy.md). Компьютер, на котором размещается пакет средств разработки (узел пакета средств разработки), должен соответствовать требованиям к оборудованию, программному обеспечению и сети. Также необходимо выбрать использование Azure Active Directory или служб федерации Active Directory. Прежде чем начинать развертывание, убедитесь в том, что выполнены предварительные требования, чтобы процесс установки прошел без проблем. 

2. [**Скачайте пакет развертывания и извлеките его содержимое**](azure-stack-run-powershell-script.md#download-and-extract-the-development-kit). Пакет развертывания можно скачать на узел пакета средств разработки или на другой компьютер. Извлекаемые файлы развертывания занимают 60 ГБ дискового пространства, поэтому использование другого компьютера помогает снизить требования к оборудованию для узла пакета средств разработки.

3. [**Подготовьте узел пакета средств разработки**](azure-stack-run-powershell-script.md) с помощью программы установки. После этого шага на узле пакета средств разработки загрузится Cloudbuilder.vhdx (виртуальный жесткий диск, содержащий загружаемую операционную систему и файлы установки Azure Stack).

4. [**Разверните пакет средств разработки**](azure-stack-run-powershell-script.md) на соответствующем узле.

5. Если в развертываемой системе Azure Stack используется Azure Active Directory, необходимо [зарегистрировать Azure Stack в Azure](azure-stack-register.md), чтобы можно было [скачивать элементы Azure Marketplace](azure-stack-download-azure-marketplace-item.md) в Azure Stack.

После выполнения этих действий вы получите среду пакета средств разработки с порталом администрирования и пользовательским порталом. Далее можно [подключиться к порталу и войти](azure-stack-connect-azure-stack.md) на него. Затем можно приступить к развертыванию поставщиков ресурсов, созданию [предложений](azure-stack-key-features.md#regions-services-plans-offers-and-subscriptions) и наполнению Azure Stack [Marketplace](azure-stack-marketplace.md).
