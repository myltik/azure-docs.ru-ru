---
title: Недостаточный размер папки TEMP по умолчанию для роли | Документация Майкрософт
description: Роль облачной службы располагает ограниченным объемом места в папке TEMP. Эта статья содержит советы о том, как предотвратить нехватку места.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 177438c4d6b416584f1797ec1ce744fc484ef688
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154534"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Недостаточный размер стандартной папки TEMP для рабочей роли или веб-роли облачной службы
Максимальный размер временного каталога по умолчанию рабочей роли или веб-роли облачной службы составляет 100 МБ, чего может оказаться недостаточно в определенный момент. В этой статье описано, как можно предотвратить нехватку места для временного каталога.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Почему мне не хватает свободного места?
В выполняемом в вашей программе коде используются стандартные переменные среды Windows: TEMP и TMP. Переменные TEMP и TMP указывают на один каталог с максимальным размером в 100 МБ. Данные, хранящиеся в этом каталоге, не хранятся на протяжении жизненного цикла облачной службы; если экземпляры роли в облачной службе перезапускаются, этот каталог очищается.

## <a name="suggestion-to-fix-the-problem"></a>Предложение по устранению проблемы
Примените один из следующих альтернативных способов.

* Настройте локальный ресурс хранилища и обращайтесь напрямую к нему, не используя TEMP или TMP. Чтобы получить доступ к локальному ресурсу хранилища из кода, запущенного в программе, вызовите метод [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) .
* Настройте локальный ресурс хранилища и задайте каталоги TEMP и TMP, чтобы указать путь к этому локальному ресурсу хранилища. Это изменение следует выполнить внутри метода [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

В следующем примере кода показано, как изменить целевые каталоги TEMP и TMP из метода OnStart:

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация
См. блог, в котором описывается, [как увеличить размер временной папки ASP.NET веб-ролей Azure](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Просмотрите дополнительные [статьи об устранении неполадок](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) в облачных службах.

Чтобы узнать, как устранять неполадки ролей облачной службы с помощью диагностических данных компьютеров Azure PaaS, изучите [серию статей в блоге Кевина Уильямсона](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
