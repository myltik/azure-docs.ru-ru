---
title: Средство оценки решений Cortana Intelligence | Документация Майкрософт
description: Ниже описаны все шаги, которые партнеру корпорации Майкрософт необходимо выполнить для публикации решений Cortana Intelligence.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams
ms.openlocfilehash: a94febdb89573930715006501b3690c0aa845b7b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836755"
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Средство оценки решений Cortana Intelligence
## <a name="overview"></a>Обзор
Средство оценки решений Cortana Intelligence можно использовать для проверки расширенных аналитических решений на соответствие рекомендациям Майкрософт. Корпорация Майкрософт с готовностью сотрудничает со своими партнерами (независимыми поставщиками программного обеспечения и системными интеграторами), чтобы предоставить клиентам, торговым посредникам и партнерам внедрения высококачественных решений. В этом руководстве будет рассмотрен процесс использования средства оценки решений с вашим решением и описаны конкретные рекомендации по проверке.

## <a name="getting-started"></a>Приступая к работе
[Скачайте](https://aka.ms/aa-evaluation-tool-download) и установите средство оценки решений Cortana Intelligence.

Предварительные требования:
- Windows 10: [официальный сайт Windows 10](https://www.microsoft.com/en-us/windows);
- Azure Powershell: [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0) (Установка и настройка Azure PowerShell).

## <a name="identifying-your-app"></a>Определение приложения
После завершения установки откройте средство и приступите к первой оценке.

![Открытие средства оценки](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Укажите идентификационные сведения о решении.

![Подключение к подписке Azure](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Подключитесь к подписке Azure и укажите группу ресурсов, содержащую приложение.

![Выбор ресурсов](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

После загрузки группы ресурсов выберите ресурсы решения и определите доступность всех ресурсов данных, таких как:
- Прием
- Потребление
- Внутренний

Мы используем эти сведения, чтобы лучше понять, как решение использует различные компоненты, а также, чтобы убедиться в том, что компоненты пользовательского интерфейса соответствуют рекомендациям.

### <a name="ingestion"></a>Прием
В этом случае прием означает все источники данных, которые используются для извлечения данных за пределами решения, или любые службы за пределами решения, которые используются для отправки в него данных.

### <a name="consumption"></a>Потребление
Потребление в данном случае означает все наборы данных, которые используются для прямой или косвенной отправки данных пользователям. Например: 
- Наборы данных, используемые в прямых запросах из Power BI.
- Наборы данных, запрашиваемые в веб-приложениях.

>[!NOTE]
Если для приема и потребления используется конкретный ресурс, выберите **Потребление**.

### <a name="internal"></a>Внутренний
Укажите все внутренние ресурсы данных, которые используются только при обработке внутри приложения.

После этого вам будет предложено предоставить действительные учетные данные для всех баз данных, указанные на предыдущем шаге:

![Настройка необходимых условий для теста](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Тестовые случаи приложений
Средство решения выполнит несколько автоматических тестов решения.

![Выполнение набора тестов](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

После выполнения тестов появится запрос на предоставление пояснений или причин, по которым решение не соответствует требованиям.

![Предоставление коммерческого обоснования](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Если решение публикуется в хранилище данных SQL Azure, для оценочных тестов потребуется также публикация в Azure Analysis Services. 

Решение может использовать виртуальные машины IaaS под управлением SQL Server Analysis Services вместо Azure Analysis Services. Это может быть приемлемой причиной неудачного выполнения теста.
## <a name="packaging-your-evaluation-results"></a>Упаковка результатов оценки
После выполнения тестовых случаев пакет оценки будет экспортирован в ZIP-файл, и вам будет предложено отправить отзыв о средстве оценки. 

Этот ZIP-файл результатов теста вашего решения нужно предоставить корпорации Майкрософт для оценки перед получением подтверждения и добавлением в AppSource.

![Градация средства оценки](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

В разделе выше этой статьи рассматриваются различные функции средства. Теперь давайте рассмотрим типы рекомендаций, которые этот инструмент оценивает.

## <a name="security-evaluation-considerations"></a>Рекомендации по оценке безопасности
### <a name="databases-should-use-azure-active-directory-authentication"></a>Базы данных должны использовать проверку подлинности Azure Active Directory
Все ресурсы Azure SQL или хранилища данных SQL Azure в решении должны поддерживать проверку подлинности Azure Active Directory (AAD). AAD представляет собой единое место для управления всеми удостоверениями и ролями.

| Дополнительные сведения | Статья |
| --- | --- |
| AAD с базой данных SQL Azure и хранилищем данных SQL | [Использование аутентификации Azure Active Directory для аутентификации с помощью базы данных SQL или хранилища данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) |
| Настройка и управление AAD | [Настройка аутентификации Azure Active Directory и управление ею с использованием базы данных SQL или хранилища данных SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) |
| Проверки подлинности веб-приложений Azure | [Проверка подлинности и авторизация в службе приложений Azure](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) |
| Настройка веб-приложений с помощью AAD | [Настройка приложения службы приложений для использования службы входа Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>Наборы данных, доступные пользователям, должны поддерживать управление доступом на основе ролей
Во время выполнения средства оценки вам будет предложено указать ресурсы отчетности и публикации. Предполагается, что эти ресурсы предназначены для использования пользователями, а не разработчиками. Чтобы убедиться, что пользователи имеют доступ только к авторизованным данным, этим ресурсам следует предоставить возможность управления доступом на основе ролей (RBAC).

В частности все следующие ресурсы Azure можно настроить с помощью RBAC и все они считаются приемлемыми.
- Общие сведения о защите HDInsight см. в статье [Основные сведения о безопасности Hadoop в присоединенных к домену кластерах HDInsight (предварительная версия)](https://docs.microsoft.com/azure/hdinsight/hdinsight-domain-joined-introduction).
- Общие сведения об Azure SQL см. в статье [Использование аутентификации Azure Active Directory для аутентификации с помощью базы данных SQL или хранилища данных SQL]( https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).
- Общие сведения о службах Azure Analysis Services см. в статье [Manage database roles and users](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users) (Управление ролями баз данных и пользователями).
- Хранилище данных SQL Azure (так как хранилище данных SQL поддерживает RBAC, мы не рекомендуем предоставлять к нему доступ пользователям).

Если вы используете другой тип ресурса, поддерживающий RBAC, укажите это в обосновании тестового случая.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Для Azure Data Lake Store следует использовать шифрование неактивных данных
Azure Data Lake Store (ADLS) по умолчанию поддерживает шифрование неактивных данных с помощью ключей шифрования, под управлением ADLS. Шифрование также можно настроить с использованием Azure Key Vault.

Дополнительные сведения об указании параметров шифрования ADLS см. в разделе [Создание учетной записи хранения озера данных Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>В Azure SQL и в хранилище данных SQL Azure следует использовать шифрование
Azure SQL и хранилище данных SQL Azure поддерживают прозрачное шифрование данных (TDE), обеспечивающее шифрование и расшифровку данных и файлов журналов в реальном времени.

| Дополнительные сведения | Статья |
| --- | --- |
| Прозрачное шифрование данных (TDE) | [Transparent Data Encryption (Прозрачное шифрование данных)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Хранилище данных Azure SQL с прозрачным шифрованием данных | [Начало работы с прозрачным шифрованием данных (TDE)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Настройка Azure SQL с прозрачным шифрованием данных | [Прозрачное шифрование данных в Базе данных SQL Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Настройка SQL Azure с шифрованием Always Encrypted | [Always Encrypted: защита конфиденциальных данных в Базе данных SQL и хранение ключей шифрования в хранилище ключей Azure](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Помимо прозрачного шифрования данных Azure SQL также поддерживает шифрование Always Encrypted — новую технологию шифрования данных, которая гарантирует, что данные шифруются не только при хранении и во время передачи между клиентом и сервером, но и при использовании данных во время выполнения команды на сервере.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Все виртуальные машины нужно развертывать из Azure Marketplace
Чтобы обеспечить приемлемый уровень безопасности в AppSource, требуется, чтобы все виртуальные машины, развернутые как часть решения Cortana Intelligence, были сертифицированы и опубликованы в Azure Marketplace.

Текущий список образов Azure Marketplace см. в [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

Сведения о том, как опубликовать образ виртуальной машины для Azure Marketplace, см. в статье [Руководство по созданию образа виртуальной машины для Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

## <a name="scalability-evaluation-considerations"></a>Рекомендации по оценке масштабируемости
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Решения Cortana Intelligence должны содержать масштабируемую платформу для больших данных
Решения Cortana Intelligence должны масштабироваться до очень больших объемов данных. В Azure это означает, что они должны содержать одну из двух платформ данных петабайтного уровня:
- Хранилище озера данных Azure
- Хранилище данных SQL Azure

Если решению не требуется поддержка данных таких размеров или в случае использования альтернативной платформы данных, укажите это в обосновании тестового случая.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Решения Cortana Intelligence должны содержать выделенные среды приема данных
Решения Cortana Intelligence не должны непосредственно вставлять данные в реляционные источники данных. Вместо этого необработанные данные должны храниться в неструктурированной среде с идемпотентными вставками или обновлениями в любом реляционном хранилище с помощью фабрики данных Azure.

Дополнительные сведения о копировании данных с помощью фабрики данных Azure см. в статье [Руководство. Создание конвейера с действием копирования с помощью Visual Studio](https://docs.microsoft.com/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>В хранилище данных SQL Azure для приема данных следует использовать PolyBase
Хранилище данных SQL поддерживает PolyBase для параллельного приема данных с высоким уровнем масштабируемости. PolyBase позволяет использовать хранилище данных SQL Azure, чтобы отправлять запросы к внешним наборам данных, хранящимся в хранилище BLOB-объектов или в Azure Data Lake Store. Это обеспечивает высокую производительность альтернативных методов массовых обновлений.

Инструкции по началу работы с PolyBase и хранилищем данных SQL Azure см. в статье [Загрузка данных в хранилище данных SQL с помощью PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

Рекомендации по работе с PolyBase и хранилищем данных SQL Azure см. в статье [Руководство по использованию PolyBase в хранилище данных SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Рекомендации по оценке доступности

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>Наборы данных, доступные пользователям, должны поддерживать большое количество одновременных пользователей
Во время выполнения средства оценки вам будет предложено указать ресурсы отчетности и публикации. Предполагается, что эти ресурсы предназначены для использования пользователями, а не разработчиками. Эти ресурсы должны поддерживать от среднего до большого количества одновременных пользователей.

В частности хранилище данных SQL Azure не должно быть единственным источником данных, доступным для пользователей. Если хранилище данных SQL Azure предоставляется как ресурс для опытных пользователей, необходимо предоставить доступ к службам Azure Analysis Services обычным пользователям.

Дополнительные сведения об ограничениях параллелизма хранилища данных SQL Azure см. в статье [Управление параллелизмом и рабочей нагрузкой в хранилище данных SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Дополнительные сведения о службах Analysis Services Azure см. в статье [Службы Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Для отработки отказа ресурсы SQL Azure должны иметь реплику только для чтения
Базы данных SQL Azure поддерживают георепликацию данных на вторичный экземпляр. Затем этот экземпляр можно использовать в качестве экземпляра отработки отказа, чтобы обеспечить высокий уровень доступности приложений.

Дополнительные сведения о георепликации в базе данных SQL Azure см. в статье [Обзор. Группы отработки отказа и активная георепликация](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).

Инструкции по настройке георепликации в SQL Azure см. в разделе [Настройка активной георепликации для базы данных SQL Azure с помощью Transact-SQL](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>В хранилище данных SQL Azure необходимо включить геоизбыточное резервное копирование
Хранилище данных SQL Azure поддерживает ежедневное резервное копирование в геоизбыточное хранилище. Георепликация гарантирует, что вы сможете восстановить хранилище данных даже при отсутствии доступа к моментальным снимкам, размещенным в основном регионе. Эта функция включена по умолчанию и не должна отключаться в решениях Cortana Intelligence.

Дополнительные сведения о резервных копиях и восстановлении хранилища данных SQL Azure см. в [этой статье ](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>Виртуальные машины необходимо настроить с помощью групп доступности
Это необходимо для минимизации влияния запланированных и незапланированных событий обслуживания.

Дополнительные сведения о виртуальных машинах Azure и группах доступности см. в статье [Управление доступностью виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Рекомендации по другим оценкам
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Приложения Cortana Intelligence должны использовать централизованное средство для оркестрации данных
Использование одного средства для планирования перемещения и преобразования данных, а также для управления этими процессами обеспечивает согласованность критически важных данных. Это обеспечивает четкую логику при управлении зависимостями, логику повторных попыток, предупреждения и ведения журнала и т. д. Мы рекомендуем использовать [фабрику данных Azure](https://docs.microsoft.com/azure/data-factory/v1/data-factory-introduction) для оркестрации данных в Azure.

Если для оркестрации данных используется средство, отличное от фабрики данных Azure, опишите, какие именно средства вы используете.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Переобучение моделей машинного обучения Azure следует выполнять с помощью фабрики данных Azure
Машинное обучение Azure предоставляет простые в использовании средства для создания и развертывания конвейеров прогнозного моделирования и машинного обучения. Тем не менее важно, чтобы развертывание этих моделей в рабочей среде не зависело от одного основного набора данных, но вместо этого адаптировалось к изменяющейся динамике реальных явлений.

Дополнительные сведения о создании переобучаемых веб-служб в AzureML см. в статье [Программное переобучение моделей машинного обучения](https://docs.microsoft.com/azure/machine-learning/machine-learning-retrain-models-programmatically).

Дополнительные сведения об автоматизации процесса обучения модели с помощью фабрики данных Azure см. в статье [Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс"](https://docs.microsoft.com/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity).

## <a name="existing-documentation"></a>Имеющаяся документация
[Сертификация Microsoft Azure](https://azure.microsoft.com/marketplace/programs/certified/)

[Сертификация Microsoft Azure: Cortana Intelligence](https://azure.microsoft.com/marketplace/programs/certified/cortana/)

