---
title: Синхронизация конфигурации сети в Службе приложений Azure | Документация Майкрософт
description: В этой статье описано, как синхронизировать конфигурацию сети для плана размещения Службы приложений Azure.
ms.service: sql-database
author: srdjan-bozovic
manager: cguyer
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1968f736dbfc24495e4e932f9c8c5955dc607133
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Синхронизация конфигурации сети для плана размещения Службы приложений Azure

Несмотря на [интеграцию приложения с виртуальной сетью Azure](../app-service/web-sites-integrate-with-vnet.md), бывает так, что установить подключение к Управляемому экземпляру не удается. В этом случае вам нужно обновить конфигурацию сети для своего плана обслуживания. 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Синхронизация конфигурации сети для плана размещения Службы приложений

Для этого выполните следующие действия:  

1. Перейдите к плану службы веб-приложений.
 
   ![План службы приложений](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Нажмите кнопку **Сети** и выберите **Щелкните здесь для управления**.
 
   ![Управление планом службы](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Выберите **виртуальную сеть** и щелкните **Синхронизировать сеть**. 
 
   ![Синхронизация сети](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Дождитесь завершения синхронизации.
  
   ![Синхронизация выполнена](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Теперь можно попробовать заново установить подключение к Управляемому экземпляру.

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о [настройке виртуальной сети для Управляемого экземпляра](sql-database-managed-instance-vnet-configuration.md).
