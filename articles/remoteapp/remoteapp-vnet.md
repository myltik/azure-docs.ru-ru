---
title: "Проверка виртуальной сети Azure для использования с Azure RemoteApp | Документация Майкрософт"
description: "Узнайте, как проверить готовность виртуальной сети Azure к использованию с Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b573ba02-4587-4be5-9821-27bd891a73b2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 30d18bdbdc6293bab5a8876fb1e503e125829e2e


---
# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Проверка виртуальной сети Azure для использования с Azure RemoteApp
> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Возможно, перед использованием виртуальной сети Azure с Azure RemoteApp ее нужно будет проверить. Это помогает избежать проблем с подключением.

Чтобы проверить виртуальную сеть Azure, выполните указанные ниже действия.

1. Создайте виртуальную машину в подсети виртуальной сети Azure, которую вы хотите использовать с Azure RemoteApp.
2. Подключитесь к этой виртуальной машине с помощью параметра **Подключиться** в портале управления.
3. Подключите виртуальную машину к тому же домену, который вы собираетесь использовать с Azure RemoteApp. При создании гибридной коллекции, которая подключается к локальной сети, подключите виртуальную машину к локальному домену.

Если это удалось, значит, виртуальная сеть Azure готова к использованию с RemoteApp.

Дополнительные сведения о полном процессе работы с гибридными коллекциями см. в следующих статьях:

* [Планирование виртуальной сети для Azure RemoteApp](remoteapp-planvnet.md)
* [Создание гибридной коллекции](remoteapp-create-hybrid-deployment.md)
* [Развертывание коллекции Azure RemoteApp для виртуальной сети Azure (с поддержкой ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)




<!--HONumber=Dec16_HO2-->


