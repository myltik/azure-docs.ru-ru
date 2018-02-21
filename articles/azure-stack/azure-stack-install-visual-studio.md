---
title: "Установка Visual Studio и подключение к Azure Stack | Документация Майкрософт"
description: "Сведения о шагах по установке Visual Studio и подключению к Azure Stack"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 5487125095f05b2fbfa9489c5b4733f61c0212d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Установка Visual Studio и подключение к Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью Visual Studio можно создавать и развертывать [шаблоны Azure Resource Manager](user/azure-stack-arm-templates.md) в Azure Stack. С помощью приведенных в этой статье инструкций можно установить Visual Studio, используя [Пакет средств разработки Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) или внешний клиент на базе Windows (при подключении через [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)). В этой статье вы выполните новую установку Visual Studio 2015 Community Edition. См. дополнительные сведения о [сосуществовании](https://msdn.microsoft.com/library/ms246609.aspx) других версий Visual Studio.

## <a name="install-visual-studio"></a>Установка Visual Studio
1. Скачайте и запустите [установщик веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).             
2. Найдите **Visual Studio Community 2015 с пакетом Microsoft Azure SDK 2.9.6**, а затем нажмите кнопку **Добавить** и **Установить**.

    ![Снимок экрана: шаги по установке установщика веб-платформы](./media/azure-stack-install-visual-studio/image1.png) 

3. Удалите средство**Microsoft Azure PowerShell**, которое установлено как часть пакета Azure SDK.

    ![Снимок экрана: добавление и удаление интерфейса программ для Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png) 

4. [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)

5. Перезагрузите компьютер после завершения установки.

## <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

1. Запустите Visual Studio.

2. В меню **Представление** выберите **Cloud Explorer**.

3. На новой панели выберите **Добавить учетную запись** и выполните вход с помощью учетных данных Azure Active Directory.  
    ![Снимок экрана: Cloud Explorer после входа и подключения к Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

После входа вы можете [развертывать шаблоны](user/azure-stack-deploy-template-visual-studio.md) или просматривать доступные типы и группы ресурсов для создания собственных шаблонов.  

## <a name="next-steps"></a>Дальнейшие действия

 - [Разработка шаблонов для Azure Stack](user/azure-stack-develop-templates.md)
