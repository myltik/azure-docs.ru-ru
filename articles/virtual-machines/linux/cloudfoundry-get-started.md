---
title: Приступая к работе с Cloud Foundry в Microsoft Azure | Документация Майкрософт
description: Запуск OSS или Pivotal Cloud Foundry в Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 42910675bcf512a3d6c76369adc9f41215420a78
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187765"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry в Azure

Cloud Foundry — это платформа как услуга (PaaS) с открытым кодом, предназначенная для сборки, развертывания и эксплуатации 12-факторных приложений, разработанных с использованием разных языков и платформ. В этом документе описываются варианты запуска Cloud Foundry в Azure и инструкции по началу работы.

## <a name="cloud-foundry-offerings"></a>Предложения Cloud Foundry

Для запуска в Azure доступны две разновидности Cloud Foundry: Cloud Foundry с открытым кодом (OSS CF) и Pivotal Cloud Foundry (PCF). OSS CF является версией Cloud Foundry с полностью [открытым кодом](https://github.com/cloudfoundry), которой управляет Cloud Foundry Foundation. Pivotal Cloud Foundry является корпоративным дистрибутивом Cloud Foundry от Pivotal Software Inc. Мы рассмотрим некоторые различия между этими двумя предложениями.

### <a name="open-source-cloud-foundry"></a>Cloud Foundry с открытым кодом

Развернуть OSS Cloud Foundry в Azure можно, сначала развернув директор BOSH, а затем — Cloud Foundry согласно [инструкциям, приведенным на сайте GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Чтобы узнать больше об использовании OSS CF, ознакомьтесь с [документацией](https://docs.cloudfoundry.org/) от Cloud Foundry Foundation.

Корпорация Майкрософт прилагает все усилия, чтобы поддерживать OSS CF с помощью следующих каналов сообщества:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>канал bosh-azure-cpi на сайте [Cloud Foundry Slack](https://slack.cloudfoundry.org/);
- [список рассылки cf-bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh);
- выпуски GitHub о [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) и [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues).

>[!NOTE]
> Уровень поддержки ресурсов Azure, таких как виртуальные машины, на которых выполняется Cloud Foundry, соответствует вашему соглашению о поддержке Azure. Максимальная поддержка сообщества относится только к компонентам Cloud Foundry.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

Pivotal Cloud Foundry содержит ту же базовую платформу, что и дистрибутив OSS, а также предоставляет защищаемый набор инструментов управления и корпоративную поддержку. Чтобы запустить PCF в Azure, необходимо получить лицензию от Pivotal. Предложение PCF в Azure Marketplace включает в себя 90-дневную пробную лицензию.

Оно содержит такие инструменты, как [Pivotal Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/) — веб-приложение, которое упрощает развертывание платформы Cloud Foundry и управление ею, и [Pivotal Apps Manager](https://docs.pivotal.io/pivotalcf/console/) — веб-приложение для управления пользователями и приложениями.

Помимо каналов поддержки, перечисленных выше для OSS CF, лицензия PCF дает право на обращение в службу поддержки Pivotal. Корпорация Майкрософт и компания Pivotal также установили рабочие процессы поддержки, которые позволяют обратиться за помощью к любой из сторон и, в зависимости от того, где обнаружена проблема, передать свой запрос соответствующим специалистам.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry придерживается методики [12-факторного приложения](https://12factor.net/), которая основывается на четком разделении процессов приложений без отслеживания состояния и резервных служб с отслеживанием состояния. Компоненты [Service Broker](https://docs.cloudfoundry.org/services/api.html) позволяют согласованно подготавливать и привязывать резервные службы к приложению. [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) предоставляет некоторые из ключевых служб Azure через этот канал, включая службу хранилища Azure и SQL Azure.

Если используется Pivotal Cloud Foundry, то Service Broker также [доступен в качестве элемента](https://docs.pivotal.io/azure-sb/installing.html) из Pivotal Network.

## <a name="related-resources"></a>Связанные ресурсы

### <a name="visual-studio-team-services-plugin"></a>Подключаемый модуль Visual Studio Team Services

Cloud Foundry идеально подходит для гибкой разработки программного обеспечения, включая использование непрерывной интеграции (CI) и непрерывной доставки (CD). Если вы используете Visual Studio Team Services (VSTS) для управления проектами и хотите установить конвейер непрерывной интеграции и непрерывной доставки, нацеленный на Cloud Foundry, то можете использовать [расширение для сборки Cloud Foundry для VSTS](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). Этот подключаемый модуль упрощает настройку и автоматизацию развертываний на платформе Cloud Foundry, работающей в Azure или в другой среде.

## <a name="next-steps"></a>Дополнительная информация

- [Pivotal Cloud Foundry on Microsoft Azure](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/) (Pivotal Cloud Foundry в Microsoft Azure)
- [Развертывание первого приложения в Cloud Foundry в Microsoft Azure](./cloudfoundry-deploy-your-first-app.md)
