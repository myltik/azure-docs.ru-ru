---
title: Параметры отладки облачных служб Azure | Документация Майкрософт
description: Отладка облачных служб Azure
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: 3d559bea8043ebcde9ffc655b617f711bfe0ea7f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292427"
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Узнайте о различных способах отладки облачной службы Azure.
В этой статье приведены ссылки на различные способы отладки облачной службы Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Отладка облачной службы Azure в Visual Studio
Вы можете сэкономить время и деньги, воспользовавшись эмулятором вычислений Azure для отладки облачной службы на локальном компьютере. Посредством локальной отладки службы перед ее развертыванием можно повысить надежность и производительность службы, не платя за время использования вычислительных ресурсов. Однако некоторые ошибки могут возникнуть только при запуске облачной службы в среде Azure. Ошибки, которые возникают только при запуске облачной службы в среде Azure, можно устранить, если включить удаленную отладку при публикации службы, а затем подключить отладчик к экземпляру роли. Дополнительные сведения см. в разделе [Отладка облачной службы на локальном компьютере](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-intellitrace"></a>Использование IntelliTrace 
Если Visual Studio Enterprise используется для написания ролей для .NET Framework 4.5, вы можете включить IntelliTrace в момент развертывания облачной службы Azure из Visual Studio. IntelliTrace предоставляет журнал, который можно использовать с Visual Studio для отладки приложения как в Azure. Дополнительные сведения см. в статье [Отладка опубликованной облачной службы с помощью IntelliTrace и Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Удаленная отладка 
Удаленную отладку облачной службы можно включить при развертывании облачной службы в Visual Studio. Если для развертывания выбрана удаленная отладка, службы удаленной отладки будут установлены на все виртуальные машины, где выполняются экземпляры роли. Такие службы (например, `msvsmon.exe`) не влияют на производительность и не требуют дополнительных расходов. Дополнительные сведения см. в разделе [Отладка облачной службы в Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Дополнительная информация
- [Отладка облачной службы или виртуальной машины Azure в Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md): сведения об отладке облачных служб Azure.