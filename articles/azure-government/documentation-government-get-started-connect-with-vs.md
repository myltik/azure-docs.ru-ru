---
title: "Подключение к Azure для государственных организаций с помощью Visual Studio | Документация Майкрософт"
description: "Сведения об управлении подпиской в Azure для государственных организаций с помощью Visual Studio."
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>Подключение с помощью Visual Studio
Разработчики используют Visual Studio, чтобы легко управлять своими подписками Azure при создании решений.  Сейчас в Visual Studio невозможно настроить подключение к Azure для государственных организаций через пользовательский интерфейс.  

### <a name="updating-visual-studio-for-azure-government"></a>Обновление Visual Studio для Azure для государственных организаций
Чтобы подключить Visual Studio к Azure для государственных организаций, необходимо обновить реестр.

1. Закройте Visual Studio.
2. Создайте текстовый файл с именем **VisualStudioForAzureGov.reg**.
3. Скопируйте и вставьте в файл **VisualStudioForAzureGov.reg** следующий текст:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Сохраните и запустите файл, дважды щелкнув его.  Вам будет предложено объединить файл с реестром.
5. Запустите Visual Studio и начните использовать [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md).

> [!NOTE]
> После установки этого раздела реестра будут доступны только подписки Azure для государственных организаций.  Настроенные ранее подписки будут по-прежнему отображаться, но не будут работать, так как Visual Studio теперь подключен к Azure для государственных организаций вместо Azure Public.  Инструкции по отмене изменений см. в следующем разделе.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Отмена подключения Visual Studio к Azure для государственных организаций
Чтобы подключить Visual Studio к Azure Public, необходимо удалить параметры реестра, обеспечивающие подключение к Azure для государственных организаций.

1. Закройте Visual Studio.
2. Создайте текстовый файл с именем **VisualStudioForAzureGov_Remove.reg**.
3. Скопируйте и вставьте в файл **VisualStudioForAzureGov_Remove.reg** следующий текст:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Сохраните и запустите файл, дважды щелкнув его.  Вам будет предложено объединить файл с реестром.
5. Запустите Visual Studio.

> [!NOTE]
> После отмены изменения этого раздела реестра подписки Azure для государственных организаций будут отображаться, но будут недоступны.  Их можно безопасно удалить.
> 
> 

