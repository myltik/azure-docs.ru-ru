---
title: "Примеры шаблонов Azure Resource Manager для службы приложений | Документация Майкрософт"
description: "Примеры шаблонов Azure Resource Manager для службы приложений"
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
editor: ggailey777
tags: azure-service-management
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 02/26/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e28a27b9a00164fcbba6eb5d3e5435548486ffa4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
---
# <a name="azure-resource-manager-templates-for-azure-web-apps"></a>Шаблоны Azure Resource Manager для веб-приложений Azure

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager. Рекомендации по предотвращению распространенных ошибок при создании шаблонов веб-приложений см. в статье [Guidance on deploying web apps with Azure Resource Manager templates](web-sites-rm-template-guidance.md) (Руководство по развертыванию веб-приложений с помощью шаблонов Azure Resource Manager).

| | |
|-|-|
|**Развертывание веб-приложения**||
| [Веб-приложение, связанное с репозиторием GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Развертывает веб-приложение Azure, которое вытягивает код из GitHub. |
| [Веб-приложение с настраиваемыми слотами развертывания](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Развертывает веб-приложение Azure с пользовательскими слотами развертывания или средами. |
|**Настройка веб-приложения**||
| [Сертификат веб-приложения из Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Развертывает сертификат веб-приложения Azure из секрета Key Vault и использует его для привязки SSL. |
| [Веб-приложение с личным доменом](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Развертывает веб-приложение Azure с пользовательским именем узла. |
| [Веб-приложение с личным доменом и SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Развертывает веб-приложение Azure с пользовательским именем узла и получает сертификат веб-приложения из Key Vault для привязки SSL. |
| [Веб-приложение с расширением GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Развертывает веб-приложение Azure с расширением сайта Golang, которое позволяет запускать веб-приложения, разработанные на сайте Golang в Azure. |
| [Веб-приложения с Java 8 и Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Развертывает веб-приложение Azure с Java 8 и Tomcat 8, что дает возможность запускать приложения Java в Azure. |
|**Веб-приложение Linux**||
| [Веб-приложение в Linux с MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Развертывает веб-приложение Azure в Linux с помощью базы данных Azure для MySQL. |
| [Веб-приложения в Linux с PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Развертывает веб-приложение Azure в Linux с помощью базы данных Azure для PostgreSQL. |
|**Веб-приложение с подключенными ресурсами**||
| [Веб-приложение с MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Развертывает веб-приложение Azure в Windows с помощью базы данных Azure для MySQL. |
| [Веб-приложение с PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Развертывает веб-приложение Azure в Windows с помощью базы данных Azure для PostgreSQL. |
| [Веб-приложение с базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Развертывает веб-приложение Azure и базу данных SQL с уровнем обслуживания "Базовый". |
| [Веб-приложение с подключением к хранилищу BLOB-объектов](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Развертывает веб-приложение Azure с помощью строки подключения к хранилищу BLOB-объектов, что дает возможность использовать хранилище из веб-приложения. |
| [Веб-приложение с кэшем Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Развертывает веб-приложение Azure с кэшем Redis. |
|**Среда службы приложений**||
| [Создание среды службы приложений версии 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Создает среду службы приложений версии 2 в виртуальной сети. |
| [Создание среды службы приложений версии 2 с адресом ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Создает среду службы приложений версии 2 в виртуальной сети с адресом частной внутренней подсистемы балансировки нагрузки. |
| [Настройка SSL-сертификата по умолчанию для ILB ASE или ILB ASE версии 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Настраивает SSL-сертификат по умолчанию для среды службы приложений ILB версии 1 или 2. |
| | |
