---
title: "Отладка облачных служб Azure | Документация Майкрософт"
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
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f10ede0a6c30c5a8ae593a5dba0f3db77134d50b


---
# <a name="debugging-cloud-services"></a>Отладка облачных служб
Для отладки приложения Azure с помощью инструментов Azure для Microsoft Visual Studio и пакета SDK Azure можно использовать различные методы.

* Приложение Azure в Visual Studio можно отладить в процессе разработки, точно так же как приложение Visual C# или Visual Basic. Дополнительные сведения см. в разделе [Отладка облачной службы на локальном компьютере](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).
* Систему диагностики Azure можно использовать для регистрации подробных сведений из кода, выполняющегося в ролях, независимо от того, запущены ли эти роли в среде разработки или в Azure. Дополнительные сведения см. в статье [Включение системы диагностики Azure в облачных службах Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).
* Если Visual Studio Enterprise используется для написания ролей для .NET Framework 4 или .NET Framework 4.5, вы можете включить IntelliTrace в момент развертывания облачной службы Azure из Visual Studio. IntelliTrace предоставляет журнал, который можно использовать с Visual Studio для отладки приложения как в Azure. Дополнительные сведения см. в статье [Отладка опубликованной облачной службы с помощью IntelliTrace и Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).
* Удаленную отладку облачной службы можно включить при развертывании облачной службы в Visual Studio. Если для развертывания выбрана удаленная отладка, службы удаленной отладки будут установлены на все виртуальные машины, где выполняются экземпляры роли. Такие службы (например, msvsmon.exe) не влияют на производительность и не требуют дополнительных расходов. Дополнительные сведения см. в разделе [Отладка облачной службы в Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).




<!--HONumber=Nov16_HO3-->


