---
title: Использование баз данных SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c2686a2d5241af46e70263d1827028aa7e9b2138
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206170"
---
# <a name="remove-the-sql-resource-provider"></a>Удаление поставщика ресурсов SQL

Чтобы удалить поставщик ресурсов SQL, сначала нужно удалить все его зависимости.

1. Найдите исходный пакет развертывания, который вы скачали ранее для этой версии адаптера поставщика ресурсов SQL.

2. Из поставщика ресурсов следует удалить все пользовательские базы данных. (При удалении пользовательских баз данных данные не удаляются). Эту задачу должны выполнить пользователи.

3. Администратору необходимо удалить серверы размещения из адаптера поставщика ресурсов SQL.

4. Администратору необходимо удалить все планы, которые ссылаются на этот адаптер поставщика ресурсов SQL.

5. Администратору необходимо удалить все номера SKU и квоты, связанные с этим адаптером поставщика ресурсов SQL.

6. Повторно запустите сценарий развертывания со следующими элементами:
    - параметр -Uninstall;
    - конечные точки Azure Resource Manager;
    - DirectoryTenantID;
    - учетные данные администратора служб.

