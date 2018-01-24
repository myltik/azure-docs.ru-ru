---
title: "Решения, связанные с развертыванием интегрированных систем Azure Stack | Документация Майкрософт"
description: "Определение решений, связанных с планированием развертывания Azure Stack с несколькими узлами."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d4aaf5af4fdb9ff5d185ef14333db4e204b9a955
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
---
# <a name="deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Решения, связанные с развертыванием интегрированных систем Azure Stack
Если вы заинтересованы в интегрированной системе Azure Stack, следует рассмотреть [некоторые рекомендации по планированию](azure-stack-deployment-planning.md) развертывания Azure Stack, а также внедрения системы в центр обработки данных. Кроме того, необходимо решить, как именно Azure Stack будет интегрироваться с гибридной облачной средой. В этой статье представлен обзор этих основных решений, включая решения, связанные с выбором модели подключения к Azure, хранилища удостоверений и модели выставления счетов.

Если вы решите приобрести интегрированную систему, ваш поставщик от изготовителя оборудования (OEM) проведет вас по большой части этапов процесса планирования. Он также выполнит фактическое развертывание.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Выбор модели подключения для развертывания Azure Stack
Можно развернуть Azure Stack с подключением к Интернету (и к Azure) или без подключения. Для максимально эффективной работы с Azure Stack, включая гибридные сценарии между Azure Stack и Azure, следует выполнить развертывание с подключением к Azure. Этот выбор определяет, какие варианты доступны для хранилища удостоверений (Azure Active Directory или службы федерации Active Directory) и модели выставления счетов (выставление счетов с оплатой по мере использования или на основе емкости), как показано на схеме и в таблице ниже. 

![Сценарии развертывания и выставления счетов Azure Stack](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> Это самое важное решение! Решение о выборе между использованием службы федерации Active Directory (AD FS) или Azure Active Directory (Azure AD) принимается один раз во время развертывания. Вы не сможете изменить хранилище удостоверений без повторного развертывания всей системы.  


|Параметры|С подключением к Azure|Без подключения к Azure|
|-----|-----|-----|
|Azure AD|![Поддерживаются](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![Поддерживаются](media/azure-stack-deployment-decisions/check.png)|![Поддерживаются](media/azure-stack-deployment-decisions/check.png)|
|Выставление счетов на основе использования|![Поддерживаются](media/azure-stack-deployment-decisions/check.png)| |
|Выставление счетов на основе емкости|![Поддерживаются](media/azure-stack-deployment-decisions/check.png)|![Поддерживаются](media/azure-stack-deployment-decisions/check.png)|
|Скачивание пакетов обновлений непосредственно в Azure Stack|![Поддерживаются](media/azure-stack-deployment-decisions/check.png)|  |

Когда вы решите, какую модель подключения к Azure использовать для развертывания Azure Stack, в зависимости от выбранной модели подключения нужно принять дополнительные решения о хранилище удостоверений и методе выставления счетов. 

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о [решениях, связанных с развертыванием Azure Stack с подключением к Azure](azure-stack-connected-deployment.md)
- Дополнительные сведения о [решениях, связанных с развертыванием Azure Stack без подключения к Azure](azure-stack-disconnected-deployment.md)
