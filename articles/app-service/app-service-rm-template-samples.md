---
title: Примеры шаблонов Azure Resource Manager для службы приложений | Документация Майкрософт
description: Примеры шаблонов Azure Resource Manager для функции "Веб-приложения службы приложений"
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
ms.openlocfilehash: 155b47fc4c664701ec0f21bdc5ae34f3d7f666ff
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29933074"
---
# <a name="azure-resource-manager-templates-for-web-apps"></a>Шаблоны Azure Resource Manager для функции "Веб-приложения"

В приведенной ниже таблице представлены ссылки на шаблоны Azure Resource Manager для функции "Веб-приложения службы приложений". Рекомендации по предотвращению распространенных ошибок при создании шаблонов веб-приложений см. в статье [Руководство по развертыванию веб-приложений с помощью шаблонов Azure Resource Manager](web-sites-rm-template-guidance.md).

| | |
|-|-|
|**Развертывание веб-приложения**||
| [Веб-приложение, связанное с репозиторием GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy)| Развертывает веб-приложение Azure, которое вытягивает код из GitHub. |
| [Веб-приложение с настраиваемыми слотами развертывания](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots)| Развертывает веб-приложение Azure с пользовательскими слотами развертывания или средами. |
|**Настройка веб-приложения**||
| [Сертификат веб-приложения из Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)| Позволяет развернуть сертификат веб-приложения Azure из секрета Azure Key Vault и использовать его для привязки SSL. |
| [Веб-приложение с личным доменом](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain)| Развертывает веб-приложение Azure с пользовательским именем узла. |
| [Веб-приложение с личным доменом и SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl)| Позволяет развернуть веб-приложение Azure с пользовательским именем узла и получить сертификат веб-приложения из Key Vault для привязки SSL. |
| [Веб-приложение с расширением GoLang](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang)| Позволяет развернуть веб-приложение Azure с расширением сайта GoLang. Вы сможете запускать веб-приложения, разработанные на сайте GoLang в Azure. |
| [Веб-приложения с Java 8 и Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat)| Позволяет развернуть веб-приложение Azure с поддержкой Java 8 и Tomcat 8. Вы сможете запускать приложения Java в Azure. |
|**Веб-приложение Linux**||
| [Веб-приложение в Linux с MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) | Позволяет развернуть веб-приложение Azure в Linux с помощью службы "База данных Azure для MySQL". |
| [Веб-приложения в Linux с PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) | Позволяет развернуть веб-приложение Azure в Linux с помощью службы "База данных Azure для PostgreSQL". |
|**Веб-приложение с подключенными ресурсами**||
| [Веб-приложение с MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql)| Позволяет развернуть веб-приложение Azure в Windows с помощью службы "База данных Azure для MySQL". |
| [Веб-приложение с PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql)| Позволяет развернуть веб-приложение Azure в Windows с помощью службы "База данных Azure для MySQL". |
| [Веб-приложение с базой данных SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)| Позволяет развернуть веб-приложение Azure и базу данных SQL с уровнем обслуживания "Базовый". |
| [Веб-приложение с подключением к хранилищу BLOB-объектов](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection)| Позволяет развернуть веб-приложение Azure со строкой подключения к хранилищу BLOB-объектов. Вы сможете использовать хранилище BLOB-объектов из веб-приложения. |
| [Веб-приложение с кэшем Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache)| Позволяет развернуть веб-приложение Azure с кэшем Redis. |
|**Среда службы приложений для PowerApps**||
| [Создание среды службы приложений версии 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) | Позволяет создать среду службы приложений версии 2 в виртуальной сети. |
| [Создание среды службы приложений версии 2 с адресом ILB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) | Позволяет создать среду службы приложений версии 2 в виртуальной сети с адресом частной внутренней подсистемы балансировки нагрузки. |
| [Настройка SSL-сертификата по умолчанию для среды службы приложений ILB версии 1 или 2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) | Позволяет настроить SSL-сертификат по умолчанию для среды службы приложений ILB версии 1 или 2. |
| | |
