---
title: Выпуск Enterprise Edition среды выполнения интеграции Azure SSIS | Документация Майкрософт
description: В этой статье описаны возможности и конфигурация Enterprise Edition среды выполнения интеграции Azure SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: 42c0c000ef661a1a256ebf49cd099a4cae2185c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="enterprise-edition-of-the-azure-ssis-integration-runtime"></a>Выпуск Enterprise Edition среды выполнения интеграции Azure SSIS

Выпуск Enterprise Edition среды выполнения интеграции Azure SSIS позволяет использовать следующие расширенные возможности и возможности уровня "Премиум":
-   компоненты отслеживания измененных данных;
-   соединители Oracle, Teradata и SAP BW;
-   соединители и преобразования SQL Server Analysis Services и Azure Analysis Services;
-   преобразования нечетких группирований и нечетких уточняющих запросов;
-   преобразования извлечения терминов и поиска по терминам.

Некоторым из этих возможностей требуется установка дополнительных компонентов для настройки среды выполнения интеграции Azure SSIS. Дополнительные сведения об установке дополнительных компонентов см. в статье [Пользовательская установка для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Корпоративные функции

| **Корпоративные функции** | **Описания** |
|---|---|
| Компоненты CDC | Источник CDC, задача управления и преобразование "Разделитель" предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. Чтобы подключиться к Oracle, необходимо установить CDC Designer и службу на другом компьютере. |
| Соединители Oracle | Диспетчер подключений, источник и назначение Oracle предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. Также необходимо установить драйвер интерфейса OCI (Oracle Call Interface) и при необходимости настроить Oracle Transport Network Substrate в среде выполнения интеграции Azure SSIS. Дополнительные сведения см. в статье о [пользовательской установке для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Соединители Teradata | Необходимо установить диспетчер подключений, источник и назначение Teradata, а также API параллельного транспортера Teradata и драйвер ODBC Teradata в среде выполнения интеграции Azure SSIS Enterprise Edition. Дополнительные сведения см. в статье о [пользовательской установке для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Соединители SAP BW | Диспетчер подключений, источник и назначение SAP BW предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. Необходимо также установить драйвер SAP BW в среде выполнения интеграции Azure SSIS. Эти соединители поддерживают SAP BW версии 7.0 или более ранних. Чтобы подключиться к более поздним версиям SAP BW или другим продуктам SAP, можно приобрести и установить соединители SAP от сторонних независимых поставщиков программного обеспечения в среде выполнения интеграции Azure SSIS. Дополнительные сведения об установке дополнительных компонентов см. в статье [Пользовательская установка для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Компоненты Analysis Services               | Назначение обучения модели интеллектуального анализа данных, назначение обработки измерений, назначение обработки секций, а также преобразование запросов интеллектуального анализа данных предварительно установлены в среде выполнения интеграции Azure SSIS. Все эти компоненты поддерживают SQL Server Analysis Services, однако Azure Analysis Services поддерживается только назначением обработки секций. Чтобы подключиться к SQL Server Analysis Services, необходимо [настроить учетные данные проверки подлинности Windows в SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Помимо этих компонентов, задача "DDL выполнения Analysis Services", задача "Обработка средствами Analysis Services" и задача "Запрос интеллектуального анализа данных" также предварительное установлены в среде выполнения интеграции Azure SSIS выпуска Standard и Enterprise Edition. |
| Преобразования нечетких группирований и нечетких уточняющих запросов  | Преобразования нечетких группирований и нечетких уточняющих запросов предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. При хранении эталонных данных эти компоненты поддерживают и сервер SQL Server, и базу данных SQL Azure. |
| Преобразования извлечения терминов и поиска по терминам | Преобразования извлечения терминов и поиска по терминам предварительно установлены в среде выполнения интеграции Azure SSIS Enterprise Edition. При хранении эталонных данных эти компоненты поддерживают и сервер SQL Server, и базу данных SQL Azure. |

## <a name="instructions"></a>Указания

1.  Загрузите и установите [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) версии 5.4 или более поздней.

2.  При подготовке или перенастройке среды выполнения интеграции Azure SSIS с помощью PowerShell, прежде чем ее запускать, выполните команду `Set-AzureRmDataFactoryV2IntegrationRuntime` с **Enterprise** в качестве значения для параметра **Edition**. Ниже приведен пример скрипта:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Дополнительная информация

-   [Пользовательская установка для среды выполнения интеграции Azure SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Разработка платных или лицензионных пользовательских компонентов для среды выполнения интеграции Azure SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)