---
title: Настройка среды выполнения интеграции Azure-SSIS для высокой производительности | Документы Microsoft
description: Узнайте, как настроить свойства среды выполнения интеграции Azure-SSIS для высокой производительности
services: data-factory
ms.date: 01/10/2018
ms.topic: article
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: ed5d1740f1d68db5ad42266c3fc2dcc983682774
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Настройка среды выполнения интеграции Azure-SSIS для высокой производительности

В этой статье описывается настройка среды выполнения интеграции Azure-SSIS для высокой производительности. Azure SSIS IR можно использовать для развертывания и запуска пакетов SQL Server Integration Services (SSIS) в Azure. Дополнительные сведения о среде выполнения интеграции Azure-SSIS IR см. в [этом разделе](concepts-integration-runtime.md#azure-ssis-integration-runtime). Сведения о развертывании и запуске пакетов SQL Server Integration Services (SSIS) в Azure см. в статье [Lift and shift SQL Server Integration Services workloads to the cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) (Перенос рабочих нагрузок SQL Server Integration Services в облако).

> [!IMPORTANT]
> Эта статья содержит результаты производительности и наблюдения за внутренним тестированием, выполняемым участниками группы разработки служб SSIS. Результаты могут различаться. Выполните тестирование до подготовки параметров конфигурации, которые могут повлиять на затраты и производительность.

## <a name="properties-to-configure"></a>Свойства, которые нужно настроить

В следующей части сценария конфигурации приведены свойства, которые можно настроить при создании среды выполнения интеграции Azure-SSIS. Полный сценарий и описание PowerShell см. в статье [Развертывание пакетов служб интеграции SQL Server (SSIS) в Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (Preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** — расположение для рабочего узла среды выполнения интеграции. Рабочий узел поддерживает постоянное подключение к базе данных каталога SSIS (SSISDB) в базе данных Azure SQL. Задайте для **AzureSSISLocation** то же расположение, что и для сервера базы данных SQL, на котором размещена SSISDB, что позволяет среде выполнения интеграции работать максимально эффективно.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Общедоступная предварительная версия фабрики данных Azure (версия 2), включая Azure-SSIS IR, поддерживает следующие параметры:
-   Standard\_A4\_v2;
-   Standard\_A8\_v2;
-   Standard\_D1\_v2;
-   Standard\_D2\_v2
-   Standard\_D3\_v2;
-   Standard\_D4\_v2.

В неофициальном внутреннем тестировании командой разработчиков служб SSIS оказалось, что устройства серии D (а не серии A) лучше использовать для выполнения пакета служб SSIS.

-   Соотношение производительности и цен серии D выше, чем серии А.
-   Пропускная способность для серий D выше, чем в сериях A (но такая же себестоимость).

### <a name="configure-for-execution-speed"></a>Настройка скорости выполнения
Если у вас немного пакетов, и вы хотите, чтобы они выполнялись быстро, используйте информацию на следующей схеме, чтобы выбрать тип виртуальной машины, подходящий для вашего сценария.

Далее предоставлены данные о выполнении одного пакета на одном рабочем узле. Этот пакет загружает 10 миллионов записей столбцов с именами и фамилиями из хранилища BLOB-объектов Azure, создает столбец с полным именем и записи с полным именем длиной более 20 символов в хранилище BLOB-объектов Azure.

![Скорость выполнения пакета среды выполнения интеграции SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Настройка общей пропускной способности

Если у вас есть много пакетов, и для вас важна общая пропускная способность, используйте информацию на следующей схеме, чтобы выбрать тип виртуальной машины, подходящий для вашего сценария.

![Максимальная общая пропускная способность среды выполнения интеграции SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** корректирует масштабируемость среды выполнения интеграции. Пропускная способность среды выполнения интеграции пропорциональна **AzureSSISNodeNumber**. Сначала задайте для **AzureSSISNodeNumber** небольшое значение, контролируйте пропускную способность среды выполнения интеграции, а затем измените значение для своего сценария. Чтобы узнать, как изменить количество рабочих узлов, см. статью [Управление средой выполнения интеграции Azure SSIS](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Если вы уже используете мощный рабочий узел для запуска пакетов, увеличение значения **AzureSSISMaxParallelExecutionsPerNode** может повысить общую пропускную способность среды выполнения интеграции. Для узлов Standard_D1_v2 поддерживаются 1–4 параллельных выполнения на каждом узле. На остальных узлах поддерживаются 1–8 параллельных выполнений.
Вы можете определить подходящее значение на основе стоимости пакета и следующих конфигураций для рабочих узлов. Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../virtual-machines/windows/sizes-general.md).

| Размер             | vCPU | Память, ГиБ | Временное хранилище (SSD): ГиБ | Максимальная пропускная способность временного хранилища: операций ввода-вывода в секунду / чтение Мбит/с / запись Мбит/с | Макс. число дисков данных / пропускная способность: операций ввода-вывода в секунду | Максимальное число сетевых адаптеров и ожидаемая производительность сети (Мбит/с) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4.    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4.    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |

Ниже приведены рекомендации по настройке правильного значения для свойства **AzureSSISMaxParallelExecutionsPerNode**. 

1. Сначала задайте для него небольшое значение.
2. Немного увеличьте его, чтобы проверить, улучшилась ли общая пропускная способность.
3. Перестаньте увеличивать значение, когда общая пропускная способность достигнет максимального значения.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

Ценовая категория **SSISDBPricingTier** предназначена для базы данных каталога SSIS (SSISDB) в базе данных Azure SQL. Этот параметр влияет на максимальное число рабочих ролей в экземпляре IR, на скорость помещения операции выполнения пакета в очередь, а также на скорость загрузки журнала выполнения.

-   Если для вас не имеет значения скорость помещения операции выполнения пакета в очередь и загрузки журнала выполнения, вы можете выбрать самый низкий уровень ценовой категории для базы данных. База данных SQL Azure с ценовой категорией "Базовый" поддерживает 8 рабочих ролей в экземпляре среды выполнения интеграции.

-   Выберите более эффективную базу данных, чем базовая, если вам требуется больше 8 рабочих ролей или больше 50 ядер. В противном случае база данных станет узким местом для экземпляра среды выполнения интеграции и отрицательно повлияет на общую производительность.

Вы также можете настроить ценовую категорию базы данных на основе информации об использовании [единицы транзакций базы данных](../sql-database/sql-database-what-is-a-dtu.md) (DTU), доступной на портале Azure.

## <a name="design-for-high-performance"></a>Проектирование для обеспечения высокой производительности
Проектирование пакета SSIS для запуска в Azure отличается от проектирования пакета для локального выполнения. Для более эффективного выполнения в Azure SSIS IR вместо объединения нескольких независимых задач в том же пакете разделите их на несколько пакетов. Создайте выполнение для каждого пакета, чтобы не приходилось ожидать выполнения каждого из них. Такой подход обеспечивает масштабируемость среды выполнения интеграции Azure-SSIS и улучшает общую пропускную способность.

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше о среде выполнения интеграции Azure-SSIS в [этом разделе](concepts-integration-runtime.md#azure-ssis-integration-runtime).