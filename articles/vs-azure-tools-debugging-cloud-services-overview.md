---
title: "Параметры отладки облачных служб Azure | Документация Майкрософт"
description: "Отладка облачных служб Azure"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: e79aa1db6c89ad1e382c7a70017f868fe186aa74
ms.lasthandoff: 03/22/2017


---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Узнайте о различных способах отладки облачной службы Azure.
В этой статье приведены ссылки на различные способы отладки облачной службы Azure. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Отладка облачной службы Azure в Visual Studio
Вы можете сэкономить время и деньги, воспользовавшись эмулятором вычислений Azure для отладки облачной службы на локальном компьютере. Посредством локальной отладки службы перед ее развертыванием можно повысить надежность и производительность службы, не платя за время использования вычислительных ресурсов. Однако некоторые ошибки могут возникнуть только при запуске облачной службы в среде Azure. Ошибки, которые возникают только при запуске облачной службы в среде Azure, можно устранить, если включить удаленную отладку при публикации службы, а затем подключить отладчик к экземпляру роли. Дополнительные сведения см. в разделе [Отладка облачной службы на локальном компьютере](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-azure-diagnostics"></a>Использование службы диагностики Azure 
Систему диагностики Azure можно использовать для регистрации подробных сведений из кода, выполняющегося в ролях, независимо от того, запущены ли эти роли в среде разработки или в Azure. Дополнительные сведения см. в статье [Включение системы диагностики Azure в облачных службах Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

## <a name="using-intellitrace"></a>Использование IntelliTrace 
Если Visual Studio Enterprise используется для написания ролей для .NET Framework 4.5, вы можете включить IntelliTrace в момент развертывания облачной службы Azure из Visual Studio. IntelliTrace предоставляет журнал, который можно использовать с Visual Studio для отладки приложения как в Azure. Дополнительные сведения см. в статье [Отладка опубликованной облачной службы с помощью IntelliTrace и Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Удаленная отладка 
Удаленную отладку облачной службы можно включить при развертывании облачной службы в Visual Studio. Если для развертывания выбрана удаленная отладка, службы удаленной отладки будут установлены на все виртуальные машины, где выполняются экземпляры роли. Такие службы (например, `msvsmon.exe`) не влияют на производительность и не требуют дополнительных расходов. Дополнительные сведения см. в разделе [Отладка облачной службы в Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>Дальнейшие действия
- [Отладка облачной службы или виртуальной машины Azure в Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md): сведения об отладке облачных служб Azure.
