---
title: "Настройка среды разработки для микрослужб Azure | Документация Майкрософт"
description: "Установите среду выполнения, пакет SDK и инструменты и создайте локальный кластер разработки. После этого вы сможете создавать приложения."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/07/2017
ms.author: ryanwi, mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: cf48dc816661fa3d61f831fb176aba048a6f5b58
ms.lasthandoff: 03/10/2017


---
# <a name="prepare-your-development-environment"></a>Подготовка среды разработки
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Чтобы создавать и запускать [приложения Service Fabric][1] на компьютере для разработки, установите среду выполнения, пакет SDK и инструменты. Вам также нужно включить выполнение сценариев Windows PowerShell, включенных в пакет SDK.

## <a name="prerequisites"></a>Предварительные требования
### <a name="supported-operating-system-versions"></a>Поддерживаемые версии операционных систем
Для разработки поддерживаются следующие операционные системы:

* Windows 7
* Windows 8 и Windows 8.1;
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 по умолчанию поставляется с Windows PowerShell версии 2.0. Для выполнения командлетов PowerShell для Service Fabric требуется PowerShell начиная с версии 3.0. В центре загрузки Майкрософт можно [скачать Windows PowerShell 5.0][powershell5-download].
> 
> 

## <a name="install-the-sdk-and-tools"></a>Установка пакета SDK и инструментов
### <a name="to-use-visual-studio-2017"></a>Для использования Visual Studio 2017
Средства Service Fabric являются частью рабочей нагрузки по разработке и управлению в Azure в Visual Studio 2017. Эту рабочую нагрузку необходимо включить при установке Visual Studio.
Кроме того, необходимо установить пакет SDK Microsoft Azure Service Fabric, используя установщик веб-платформы.

* [Установка пакета SDK Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Для использования Visual Studio 2015 (требуется Visual Studio 2015 с обновлением 2 или более поздней версии)
Для Visual Studio 2015 средства Service Fabric устанавливаются вместе с пакетом SDK с помощью установщика веб-платформы:

* [Установка пакета SDK и средств Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Только установка пакета SDK
Если вам требуется только пакет SDK, можно установить этот пакет:
* [Установка пакета SDK Microsoft Azure Service Fabric][core-sdk]

> [!WARNING]
> Во время установки с использованием этих ссылок для запуска или при использовании этих ссылок в браузере Chrome у клиентов возникают ошибки. Это известные проблемы в установщике веб-платформы. Идет работа над их устранением.  Попробуйте следующие обходные пути.
>- Перейдите по приведенным выше ссылкам с помощью браузеров Internet Explorer или Edge.
>- Запустите установщик веб-платформы из меню "Пуск", найдите Service Fabric и установите пакет SDK.
> 
> Приносим извинения за неудобства. 

Текущие версии:
* пакет SDK для Service Fabric 2.4.164;
* среда выполнения Service Fabric 5.4.164;
* средства Visual Studio 2015 1.4.50124.

Список поддерживаемых версий см. в статье [Azure Service Fabric support options](service-fabric-support.md) (Варианты поддержки Azure Service Fabric).

## <a name="enable-powershell-script-execution"></a>Включение сценариев PowerShell
Для создания локального кластера разработки и развертывания приложений из Visual Studio в Service Fabric используются сценарии Windows PowerShell. По умолчанию ОС Windows блокирует выполнение этих сценариев. Чтобы включить их, необходимо изменить политику выполнения PowerShell. Для этого запустите PowerShell с правами администратора и введите следующую команду:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Дальнейшие действия
Среда разработки настроена, и вы готовы к созданию и запуску собственных приложений.

* [Создание первого приложения Service Fabric в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
* [Информация о развертывании приложений в локальном кластере и управлении ими](service-fabric-get-started-with-a-local-cluster.md)
* [Информация о моделях программирования: Reliable Services и Reliable Actors](service-fabric-choose-framework.md)
* [Ознакомление с примерами кода Service Fabric на GitHub](https://aka.ms/servicefabricsamples)
* [Визуализация кластера с помощью обозревателя Service Fabric](service-fabric-visualizing-your-cluster.md)
* [Используйте схему обучения Service Fabric, чтобы получить общие сведения о платформе](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Страница кампании Service Fabric"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Ссылка на WebPI VS 2015"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Ссылка на WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Ссылка на WebPI Core SDK"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

