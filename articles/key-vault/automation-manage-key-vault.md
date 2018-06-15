---
title: Управление хранилищем ключей Azure с помощью службы автоматизации Azure | Документация Майкрософт
description: Сведения об использовании службы автоматизации Azure для управления хранилищем ключей Azure.
services: Key-Vault, automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.openlocfilehash: b5f8a1b826717d51729b0bb621bf26e35d4bdd36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406880"
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Управление хранилищем ключей Azure с помощью службы автоматизации Azure
В этом руководстве будет представлена служба автоматизации Azure и показано, как ее использовать для упрощения управления ключами и секретами в хранилище ключей Azure.

## <a name="what-is-azure-automation"></a>Что такое служба автоматизации Azure
[Служба автоматизации Azure](../automation/automation-intro.md) — это служба Azure, которая упрощает управление облаком благодаря автоматизации процессов и настройке требуемого состояния. С помощью службы автоматизации Azure повторяющиеся задачи, которые выполняются вручную, требуют много времени и подвержены ошибкам, можно автоматизировать для повышения надежности, эффективности и экономии времени в вашей организации.

Служба автоматизации Azure предоставляет высоконадежную и высокодоступную подсистему выполнения рабочих процессов, которая масштабируется в соответствии с вашими задачами. В службе автоматизации Azure процессы можно запустить вручную, в сторонних системах или по расписанию, чтобы все задачи выполнялись в нужное время.

Уменьшите операционные затраты и освободите ИТ-сотрудников и DevOps для работы над повышением бизнес-ценности ПО и автоматизации задач управления облаком в службе автоматизации Azure.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Как служба автоматизации Azure помогает управлять хранилищем ключей Azure?
Хранилищем ключей можно управлять в службе автоматизации Azure с помощью [командлетов хранилища ключей AzureRM](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) и [командлетов классического хранилища ключей Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx). Модуль Azure для управления классическим хранилищем ключей автоматически доступен в службе автоматизации Azure, и вы можете импортировать [модуль AzureRM-KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) в службу автоматизации Azure. Это позволит выполнять многие задачи управления хранилищем ключей в службе. Вы также можете связать эти командлеты в службе автоматизации Azure с командлетами для других служб Azure, чтобы автоматизировать сложные задачи в службах Azure и системах сторонних производителей.

С помощью командлетов хранилища ключей Azure можно выполнять следующие задачи: 

* создавать и настраивать хранилище ключей;
* создавать или импортировать ключи;
* создавать или обновлять секреты;
* обновлять атрибуты ключей;
* получать ключи или секреты;
* удалять ключи или секреты.

Вот несколько примеров использования PowerShell для управления хранилищем ключей.  

* [Azure Key Vault - Step by Step (Хранилище ключей Azure: шаг за шагом)](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Setting Up and Configuring an Azure Key Vault (Установка и настройка хранилища ключей Azure)](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы познакомились с основами службы автоматизации Azure и способами ее использования для управления хранилищем ключей Azure, пройдите по ссылкам, чтобы получить дополнительные сведения о службе автоматизации Azure.

* Изучите [руководство по началу работы](../automation/automation-first-runbook-graphical.md)со службой автоматизации Azure.
* Изучите раздел [Скрипты PowerShell хранилища ключей Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

