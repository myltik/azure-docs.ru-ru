<properties
   pageTitle="Общие сведения о том, как создать и развернуть предложение в Marketplace | Microsoft Azure"
   description="Описание шагов, необходимых для того, чтобы стать утвержденным разработчиком Майкрософт и создавать и развертывать образ виртуальной машины, шаблон, службу данных и службу разработчика в Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/17/2015"
   ms.author="hascipio" />

# Как опубликовать предложение в Microsoft Azure Marketplace
Эта статья поможет разработчику создать и развернуть решение в Azure Marketplace, чтобы другие клиенты и партнеры Azure могли приобрести и использовать это решение.

Первое, что нужно сделать в качестве издателя, — определить тип решений, которые предлагает ваша компания. Azure Marketplace поддерживает несколько типов решений, и действия для публикации каждого из типов решений в Marketplace немного отличаются друг от друга.

Типы решений:

- Образ виртуальной машины
- Служба разработчика
- Служба данных
- Шаблон решения

Некоторые действия являются общими для различных типов решений. В этой статье представлен короткий обзор шагов, которые необходимо выполнить для каждого типа решения.

> [AZURE.NOTE]Перед началом работы в Azure Marketplace вы должны пройти предварительное утверждение. Не относится к издателям служб данных.

||Образ виртуальной машины |Служба разработчика | Служба данных | Шаблон решения | |----|----|----|----|----| | **Получение предварительного одобрения** | [Сертифицировано Microsoft Azure][link-certification] | [Сертифицировано Microsoft Azure][link-certification] | н/д | [Сертифицировано Microsoft Azure][link-certification] | | **Шаг 1. Регистрация учетной записи разработчика** | [Учетная запись разработчика Майкрософт: создание и регистрация][link-accts] | [Учетная запись разработчика Майкрософт: создание и регистрация][link-accts] | [Учетная запись разработчика Майкрософт: создание и регистрация][link-accts] | [Учетная запись разработчика Майкрософт: создание и регистрация][link-accts] | |**Шаг 2. Создание предложения**| [Общие нетехнические предварительные требования](marketplace-publishing-pre-requisites.md)| [Общие нетехнические предварительные требования](marketplace-publishing-pre-requisites.md)| [Общие нетехнические предварительные требования](marketplace-publishing-pre-requisites.md)| [Общие нетехнические предварительные требования](marketplace-publishing-pre-requisites.md)| || [Технические предварительные требования виртуальной машины][link-single-vm-prereq] | [Технические предварительные требования службы разработчика](marketplace-publishing-dev-service-creation-prerequisites.md) | [Технические предварительные требования службы данных](marketplace-publishing-data-service-creation-prerequisites.md) | [Технические предварительные требования шаблона решения](marketplace-publishing-solution-template-creation-prerequisites.md) | ||[Руководство по публикации образа виртуальной машины][link-single-vm] | [Руководство по публикации службы разработчика](marketplace-publishing-dev-service-creation.md) | [Руководство по публикации службы данных](marketplace-publishing-data-service-creation.md) | [Руководство по публикации шаблона решения](marketplace-publishing-solution-template-creation.md) | || [Руководство по продвижению содержимого Azure Marketplace][link-pushstaging] | [Руководство по продвижению содержимого Azure Marketplace][link-pushstaging] | [Руководство по продвижению содержимого Azure Marketplace][link-pushstaging] | [Руководство по продвижению содержимого Azure Marketplace][link-pushstaging] | | **Шаг 3. Отправка предложения на промежуточный этап** | [Тестирование предложения виртуальной машины на промежуточном этапе](marketplace-publishing-vm-image-test-in-staging.md) | [Тестирование предложения службы разработчика на промежуточном этапе](marketplace-publishing-dev-service-test-in-staging.md) | [Тестирование предложения службы данных на промежуточном этапе](marketplace-publishing-data-service-test-in-staging.md) | [Тестирование шаблона решения на промежуточном этапе](marketplace-publishing-solution-template-test-in-staging.md) | | **Шаг 4. Развертывание предложения в Marketplace** | [Развертывание предложения в Marketplace][link-pushprod] | [Развертывание предложения в Marketplace][link-pushprod] | [Развертывание предложения в Marketplace][link-pushprod] | [Развертывание предложения в Marketplace][link-pushprod] |

## Поддержка
- [Поддержка издателей][suppt-general]
- [Основные сведения об отчетах Seller Insights][suppt-rpt-insights]
- [Основные сведения об отчетах о выплатах][suppt-rpt-payouts]
- [Решение распространенных проблем с публикацией в Marketplace][suppt-common]

## Дополнительные ресурсы
- Дополнительные сведения об используемых порталах см. на странице [Порталы, которые вам потребуются](marketplace-publishing-portals.md)

**Виртуальные машины**

- [Настройка Azure PowerShell](marketplace-publishing-powershell-setup.md)
- [Локальное создание образа виртуальной машины](marketplace-publishing-vm-image-creation-on-premise.md)
- [Создание виртуальной машины под управлением Windows на портале предварительной версии Azure](../virtual-machines-windows-tutorial/)

**Службы данных**

- [Сопоставление OData для службы данных](marketplace-publishing-data-service-creation-odata-mapping.md)
- [Узлы сопоставления OData для службы данных](marketplace-publishing-data-service-creation-odata-mapping-nodes.md)
- [Примеры сопоставления OData для службы данных](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

[suppt-general]: marketplace-publishing-get-publisher-support.md
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md
[suppt-common]: marketplace-publishing-support-common-issues.md
[link-certification]: marketplace-publishing-azure-certification.md
[link-accts]: marketplace-publishing-accounts-creation-registration.md
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-pushprod]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0114_2016-->