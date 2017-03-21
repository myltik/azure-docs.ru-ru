---
title: "Управление службами Azure Analysis Services с помощью PowerShell | Документация Майкрософт"
description: "Описывается управление службами Azure Analysis Services с помощью PowerShell."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: c0dd85570f052c4a9651faecbaeb3eaa181f9017
ms.openlocfilehash: b00b2cf1d3a54bba716d7470643aa1a5e6a58633
ms.lasthandoff: 03/01/2017


---

# <a name="manage-azure-analysis-services-with-powershell"></a>Управление службами Azure Analysis Services с помощью PowerShell

В этой статье описаны командлеты PowerShell, используемые для выполнения задач управления базами данных и сервером служб Azure Analysis Services. 

Для выполнения таких задач управления сервером, как создание или удаление сервера, приостановка или возобновление работы сервера или изменение уровня обслуживания (уровня служб), используются командлеты Azure Resource Manager (AzureRM). Для выполнения других задач управления базами данных, таких как добавление или удаление участников роли, обработка или секционирование, используются те же командлеты в модуле [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx), что и в службах SQL Server Analysis Services.

## <a name="permissions"></a>Разрешения
Для большинства задач PowerShell требуется, чтобы у пользователя были привилегии администратора на сервере служб Analysis Services, которым он управляет. Запланированные задачи PowerShell являются автоматическими операциями. У учетной записи, запускающей планировщик, должны быть привилегии администратора на сервере служб Analysis Services. 

Для выполнения операций с сервером с использованием командлетов AzureRm учетная запись или планировщик учетной записи должны также принадлежать к роли владельца для данного ресурса (указывается в настройках [управления доступом на основе ролей (RBAC) в Azure](../active-directory/role-based-access-control-what-is.md)). 

## <a name="server-operations"></a>Операции с сервером 
Командлеты служб Azure Analysis Services включены в модуль компонентов [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Для установки модулей командлетов AzureRM ознакомьтесь с [командлетами Azure Resource Manager](https://docs.microsoft.com/powershell/resourcemanager/) в коллекции PowerShell.

|Командлет|Описание| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/get-azurermanalysisservicesserver)|Возвращает сведения об экземпляре сервера.|  
|[New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/new-azurermanalysisservicesserver)|Создает новый экземпляр сервера.|
|[Remove-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/remove-azurermanalysisservicesserver)|Удаляет экземпляр сервера.|  
|[Suspend-AzureRmAnalysisServicesServe](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/suspend-azurermanalysisservicesserver)|Приостанавливает работу экземпляра сервера.| 
|[Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/resume-azurermanalysisservicesserver)|Возобновляет работу экземпляра сервера.|  
|[Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/set-azurermanalysisservicesserver)|Изменяет экземпляр сервера.|   
|[Test-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/test-azurermanalysisservicesserver)|Проверяет существование экземпляра сервера.| 

## <a name="database-operations"></a>Операции с базой данных
Для операций с базами данных служб Azure Analysis Services используется тот же модуль [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx), что и для служб SQL Server Analysis Services. Однако для предварительной версии служб Azure Analysis Services поддерживаются не все командлеты. 

Модуль SQLASCMDLETS предоставляет командлеты для конкретных задач управления базой данных, а также командлет общего назначения Invoke-ASCmd, который принимает запрос TMSL или сценарий. Для предварительной версии служб Azure Analysis Services поддерживаются следующие командлеты из модуля SQLASCMDLETS.
  
|Командлет|Описание|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Добавление участника в роль базы данных.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Удаление участника из роли базы данных.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Выполнение сценария TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Обработка базы данных.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Обработка секции.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Обработка таблицы.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Объединение секции.|  
  

## <a name="related-information"></a>Связанные сведения
* [Создание скриптов PowerShell в службах Analysis Services](https://msdn.microsoft.com/library/hh213141.aspx)
* [Tabular Model Programming for Compatibility Level 1200](https://msdn.microsoft.com/library/mt712541.aspx) (Программирование табличной модели для уровня совместимости 1200)
