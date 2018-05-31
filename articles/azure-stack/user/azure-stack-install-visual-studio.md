---
title: Установка Visual Studio и подключение к Azure Stack | Документация Майкрософт
description: Сведения о шагах по установке Visual Studio и подключению к Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 3eaefbe011c4d98fe9a76d4f277a76a2f167b191
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301899"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Установка Visual Studio и подключение к Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью Visual Studio можно создавать и развертывать [шаблоны Azure Resource Manager](azure-stack-arm-templates.md) в Azure Stack. С помощью приведенных в этой статье инструкций можно установить Visual Studio, используя [Пакет средств разработки Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) или внешний клиент на базе Windows (при подключении через [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)). В этой статье описывается новая установка Visual Studio 2015 Community Edition. Прочитайте больше о [сосуществовании](https://msdn.microsoft.com/library/ms246609.aspx) с другими версиями Visual Studio.

## <a name="install-visual-studio"></a>Установка Visual Studio

1. Скачайте и запустите [установщик веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).
2. Найдите **Visual Studio Community 2015 с пакетом SDK для Microsoft Azure 2.9.6**, а затем щелкните **Добавить** и **Установить**.

    ![Снимок экрана: шаги по установке установщика веб-платформы](./media/azure-stack-install-visual-studio/image1.png)

3. Удалите компонент **Microsoft Azure PowerShell**, который установлен как часть пакета SDK для Azure.

    ![Снимок экрана: окно "Программы и компоненты" для Azure PowerShell](./media/azure-stack-install-visual-studio/image2.png)

4. [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)

5. Перезагрузите компьютер после завершения установки.

## <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

1. Запустите Visual Studio.

2. В меню **Представление** выберите **Cloud Explorer**.

3. На новой панели выберите **Добавить учетную запись** и выполните вход с помощью учетных данных Azure Active Directory.
    ![Снимок экрана: Cloud Explorer после входа и подключения к Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

После входа вы можете [развертывать шаблоны](azure-stack-deploy-template-visual-studio.md) или просматривать доступные типы и группы ресурсов для создания собственных шаблонов.

## <a name="next-steps"></a>Дополнительная информация

* [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
