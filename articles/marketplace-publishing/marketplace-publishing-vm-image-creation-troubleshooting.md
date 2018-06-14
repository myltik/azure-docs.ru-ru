---
title: Как устранить распространенные неполадки, возникающие в процессе создания виртуального жесткого диска | Документация Майкрософт
description: Ответы на распространенные вопросы об устранении неполадок, а также о проблемах, возникающих в процессе создания виртуального жесткого диска.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: mbaldwin
ms.openlocfilehash: 65361ad5bd7c3311c428b64b8476ec8f2ea2d17b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29941608"
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Как устранить распространенные неполадки, возникающие в процессе создания виртуального жесткого диска
Сведения, приведенные в этой статье, помогут издателям и соадминистраторам решить проблемы, возникающие в процессе публикации решений lkz виртуальных машин или управления ими.

1. Как изменить имя узла?
   
    После создания виртуальной машины имя узла изменить невозможно.
2. Как сбросить службу удаленного рабочего стола или ее пароль для входа в систему?
   
   * [Справочные материалы для виртуальной машины Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Справочные материалы для виртуальной машины Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Как создать сертификаты SSH?
   
   Дополнительные сведения см. по ссылке: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Как настроить открытый сертификат VPN?
   
   Дополнительные сведения см. по ссылке: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Каковы условия предоставления поддержки для ПО Microsoft Server, запущенного в среде виртуальной машины Microsoft Azure (инфраструктура как услуга)?
   
   Дополнительные сведения см. по ссылке: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Имеют ли виртуальные машины какой-либо уникальный идентификатор?
   
   В каждой виртуальной машине Azure зашифрован уникальный идентификатор. Дополнительные сведения см. в блоге и документации.
7. Как выполняется управление расширением пользовательских скриптов при запуске виртуальной машины?
   
   Дополнительные сведения см. по ссылке: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Как создать виртуальную машину на портале Azure, используя виртуальный жесткий диск, загруженный в хранилище класса Premium?
   
   В настоящее время эта функция не поддерживается.
9. Поддерживаются ли 32-разрядные приложения в Azure Marketplace?
   
   Сведения о политике поддержки см. здесь: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672).
10. Каждый раз при попытке создать образ виртуальных жестких дисков в PowerShell отображается следующее сообщение об ошибке: "VHD уже зарегистрирован в репозитории образов как ресурс". Я не создавал образы раньше, и в Azure отсутствует образ с таким именем. Как решить эту проблему?
    
    Это обычно происходит, если пользователь подготовил виртуальную машину из виртуального жесткого диска, который заблокирован. Проверьте, не использовался ли этот виртуальный жесткий диск для выделения виртуальной машины. Если эта ошибка по-прежнему возникает, отправьте запрос в службу поддержки на портале публикации или перейдите по ссылке (дополнительные сведения см. в ответе на 11 вопрос).