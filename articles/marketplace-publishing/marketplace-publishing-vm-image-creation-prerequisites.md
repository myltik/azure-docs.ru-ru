---
title: Технические компоненты, необходимые для создания образа виртуальной машины для Azure Marketplace | Документация Майкрософт
description: Ознакомьтесь с требованиями по созданию и разработке образа виртуальной машины для Azure Marketplace, предназначенного для продажи.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: cf1f061c28dd0c106823d34ad39aac5e577c8b41
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29936661"
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Технические компоненты, необходимые для создания образа виртуальной машины для Azure Marketplace
Прежде чем начать работу, внимательно прочтите описание и разберитесь, где и зачем выполняется каждый шаг. Постарайтесь подготовить максимально подробные сведения о своей компании и другие данные, загрузить необходимые средства и/или создать технические компоненты до того, как начнете создавать предложение. Все эти компоненты описаны в данной статье.  

## <a name="download-needed-tools--applications"></a>Загрузка необходимых средств и приложений
К началу работу необходимо подготовить следующее:

* в зависимости от целевой операционной системы установите [командлеты Azure PowerShell](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) или [программу командной строки Linux](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) со страницы [Загрузки](https://azure.microsoft.com/downloads/);
* установите Azure Storage Explorer из CodePlex;
* скачайте и установите средство проверки сертификации для Azure Certified:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Для запуска инструмента сертификации потребуется компьютер под управлением Windows. Если у вас нет такого компьютера, запустите средство сертификации на виртуальной машине Windows в Azure.

## <a name="platforms-supported"></a>Поддерживаемые платформы
Виртуальные машины Azure можно разрабатывать на базе Windows или Linux. На некоторых этапах публикации, например при создании виртуального жесткого диска (VHD), совместимого с Azure, используемые инструменты и выполняемые действия зависят от операционной системы.  

* При использовании операционной системы Linux см. раздел "Создание виртуального жесткого диска, совместимого с Azure (для Linux)" [руководства по публикации образов виртуальных машин](marketplace-publishing-vm-image-creation.md).
* При использовании операционной системы Windows см. раздел "Создание виртуального жесткого диска, совместимого с Azure (на основе Windows)" [руководства по публикации образов виртуальных машин](marketplace-publishing-vm-image-creation.md).

> [!NOTE]
> Доступ к компьютеру под управлением Windows необходим для:
> 
> * запуска инструмента проверки сертификации;
> * создания подписанного URL-адреса VHD для передачи сертификации VHD.
> 
> 

## <a name="develop-your-vhd"></a>Разработка VHD
Диски VHD Azure можно разрабатывать в облаке или локально.

* Разработка в облаке означает, что все этапы разработки выполняются удаленно на VHD, который находится в Azure.
* Для локальной разработки VHD необходимо загрузить и разработать в локальной инфраструктуре. Такой вариант возможен, но не рекомендуется. Обратите внимание на то, что локальная разработка для Windows и SQL требует соответствующих локальных лицензионных ключей. Невозможно добавить или установить SQL Server после создания виртуальной машины. Кроме того, в вашем предложении должен использоваться утвержденный образ SQL с портала Azure. При выборе локальной разработки некоторые действия нужно будет выполнить не так, как при разработке в облаке. Соответствующие сведения см. в статье [Локальная разработка образа виртуальной машины для Azure Marketplace](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
