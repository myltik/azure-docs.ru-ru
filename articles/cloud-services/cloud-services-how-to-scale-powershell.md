---
title: "Масштабирование облачной службы Azure в Windows PowerShell | Документация Майкрософт"
description: "(Классическая модель.) Узнайте, как использовать PowerShell для масштабирования веб-роли или рабочей роли в Azure."
services: cloud-services
documentationcenter: 
author: seanmck
manager: timlt
editor: 
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 4acea8e7f4714754e6b3e54e9aaa622219ca9f7f
ms.openlocfilehash: f16d981a0dc33295c1981d9c282beb1d3243d9ef


---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Как масштабировать облачную службу в PowerShell

Масштабировать веб-роль или рабочую роль с помощью Windows PowerShell можно, добавляя или удаляя экземпляры.  

## <a name="log-in-to-azure"></a>Вход в Azure

Прежде чем выполнять какие-либо операции с подпиской с помощью PowerShell, вы должны войти в систему.

```powershell
Add-AzureAccount
```

Если с вашей учетной записью связано несколько подписок, может потребоваться сменить текущую подписку. Это зависит от того, где находится ваша облачная служба. Чтобы проверить, какая подписка используется в данный момент, выполните следующую команду.

```powershell
Get-AzureSubscription -Current
```

Если необходимо сменить текущую подписку, выполните приведенную ниже команду.

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Проверка текущего количества экземпляров для роли

Чтобы проверить текущее состояние роли, выполните приведенную команду.

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Должна отобразиться информация о роли, включая версию ее текущей операционной системы и число экземпляров. В этом случае у роли имеется один экземпляр.

![Информация о роли](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Развертывание роли путем добавления дополнительных экземпляров

Чтобы развернуть роль, передайте нужное число экземпляров как параметр **Count** командлета **Set-AzureRole**.

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Командлет мгновенно блокируется, пока готовятся и запускаются новые экземпляры. Если в это время открыть новое окно PowerShell и вызвать **Get-AzureRole**, как показано выше, то вы увидите новое целевое число экземпляров. И если просмотреть состояние роли на портале, то вы увидите, что запускается новый экземпляр.

![Запуск экземпляра виртуальной машины на портале](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

После запуска новых экземпляров командлет отобразит сообщение об успешном выполнении.

![Успешное увеличение числа экземпляров роли](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Свертывание роли путем удаления экземпляров

Точно так же можно свернуть роль, удаляя экземпляры. Задайте для параметра **Count** командлета **Set-AzureRole** число экземпляров, которые должны остаться после операции свертывания.

## <a name="next-steps"></a>Дальнейшие действия

Настроить автомасштабирование для облачных служб из PowerShell невозможно. Об этом можно узнать в разделе [Автомасштабирование облачной службы](cloud-services-how-to-scale-portal.md).



<!--HONumber=Dec16_HO1-->


