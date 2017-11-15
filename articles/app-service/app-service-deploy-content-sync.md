---
title: "Синхронизация содержимого из папки в облаке со службами приложений Azure"
description: "Узнайте, как развертывать приложение в службе приложений Azure с помощью синхронизации содержимого из папки в облаке."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 04d1d226093f131a521f32f47c333ff9aefc6f3b
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Синхронизация содержимого из папки в облаке со службами приложений Azure
В этом учебнике показано, как выполнить развертывание в [службе приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) , синхронизируя содержимое из популярных облачных служб хранилища, например Dropbox и OneDrive. 

## <a name="overview"></a>Обзор развертывания синхронизации содержимого
Развертывание синхронизации содержимого по запросу основано на [механизме развертывания Kudu](https://github.com/projectkudu/kudu/wiki), который интегрирован в службу приложений. На [портале Azure](https://portal.azure.com) в облачном хранилище можно указать специальную папку, в которой вы будете работать с кодом приложения и содержимым. Затем эту папку можно синхронизировать со службой приложений нажатием всего лишь одной кнопки. При синхронизации содержимого для выполнения сборки и развертывания используется процесс Kudu. 

## <a name="contentsync"></a>Как включить развертывание синхронизации содержимого
Чтобы включить синхронизацию содержимого на [портале Azure](https://portal.azure.com), выполните следующее.

1. На портале Azure в колонке приложения щелкните **Параметры** > **Источник развертывания**. Щелкните **Выбор источника** и в качестве источника развертывания выберите **OneDrive** или **Dropbox**. 
   
    ![Синхронизация содержимого](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > Из-за основных различий в интерфейсах API **OneDrive для бизнеса** пока не поддерживается. 
   > 
   > 
2. Завершите процесс авторизации, чтобы разрешить службе приложений доступ к конкретному предварительно определенному пути OneDrive или Dropbox, где будет храниться все ваше содержимое службы приложений.  
    После авторизации платформа службы приложений предоставит возможность создать папку содержимого по указанному пути либо выбрать существующую папку содержимого по этому пути. Ниже перечислены назначенные пути к содержимому учетных записей хранения в облаке, используемые для синхронизации службы приложений.  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. После первоначальной синхронизации содержимого ее можно будет запускать по требованию с портала Azure. Журнал развертываний доступен в колонке **Развертывания** .
   
    ![Журнал развертываний](./media/app-service-deploy-content-sync/onedrive_sync.png)

Дополнительные сведения о развертывании Dropbox можно найти в разделе [New! Deploy to Windows Azure Web Sites from Dropbox] (Новинка! Развертывание в службе "Веб-сайты Microsoft Azure" из Dropbox) (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

