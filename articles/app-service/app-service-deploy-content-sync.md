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
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 8e132e4d4a65588d57e3cfb969e785f5a164206c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Синхронизация содержимого из папки в облаке со службами приложений Azure
В этом руководстве показано, как синхронизировать содержимое со [службой приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) из популярных облачных служб хранилища, например Dropbox и OneDrive. 

## <a name="overview"></a>Обзор развертывания синхронизации содержимого
Развертывание синхронизации содержимого по запросу основано на [механизме развертывания Kudu](https://github.com/projectkudu/kudu/wiki), который интегрирован в службу приложений. На [портале Azure](https://portal.azure.com) в облачном хранилище можно указать специальную папку, в которой вы будете работать с кодом приложения и содержимым. Затем, нажав кнопку, эту папку можно синхронизировать со службой приложений. При синхронизации содержимого для выполнения сборки и развертывания используется процесс Kudu. 

## <a name="contentsync"></a>Как включить развертывание синхронизации содержимого
Чтобы включить синхронизацию содержимого на [портале Azure](https://portal.azure.com), сделайте следующее.

1. На портале Azure на странице приложения щелкните **Параметры** > **Источник развертывания**. Щелкните **Выбор источника** и в качестве источника развертывания выберите **OneDrive** или **Dropbox**. 
   
    ![Синхронизация содержимого](./media/app-service-deploy-content-sync/deployment_source.png)
   
   > [!NOTE]
   > Из-за основных различий в интерфейсах API **OneDrive для бизнеса** пока не поддерживается. 
   > 
   > 
2. Завершите процесс авторизации, чтобы разрешить службе приложений доступ к конкретному предварительно определенному пути OneDrive или Dropbox, где будет храниться все ваше содержимое службы приложений. После авторизации платформа службы приложений предоставит возможность создать папку содержимого по указанному пути либо выбрать имеющуюся папку содержимого по этому пути. Ниже перечислены назначенные пути к содержимому учетных записей хранения в облаке, используемые для синхронизации службы приложений.  
   
   * **OneDrive**: `Apps\Azure Web Apps` 
   * **Dropbox**: `Dropbox\Apps\Azure`
3. После первоначальной синхронизации содержимого ее можно будет запускать по требованию на портале Azure. Журнал развертываний доступен на странице **Развертывания**.
   
    ![Журнал развертываний](./media/app-service-deploy-content-sync/onedrive_sync.png)

Дополнительные сведения о развертывании Dropbox можно найти в разделе [New! Deploy to Windows Azure Web Sites from Dropbox] (Новинка! Развертывание в службе "Веб-сайты Microsoft Azure" из Dropbox) (https://azure.microsoft.com/en-in/blog/new-deploy-to-windows-azure-web-sites-from-dropbox/).

