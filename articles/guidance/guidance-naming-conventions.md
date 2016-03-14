<properties
   pageTitle="Рекомендуемые соглашения об именовании для ресурсов Azure | Руководство | Microsoft Azure"
   description="Рекомендуемые соглашения об именовании для ресурсов Azure. Именование виртуальных машин, учетных записей хранения, сетей, виртуальных сетей, подсетей и других сущностей Azure"
   services=""
   documentationCenter="na"
   authors="masimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/01/2016"
   ms.author="masimms"/>
   
# Рекомендуемые соглашения об именовании для ресурсов Azure

Выбор имени для любого ресурса в Microsoft Azure является важным по следующим причинам.

- Позже будет сложно изменить имя.
- Имена должны соответствовать требованиям к типу ресурса, который они называют.

Кроме того, находить ресурсы будет проще, если упорядочить соглашения об именовании. Упорядоченные соглашения также помогут понять роль ресурса в решении.

В данной статье кратко изложены правила именования и ограничения для имен ресурсов Azure, а также представлен базовый набор рекомендаций по соглашениям об именовании. Эти рекомендации можно использовать как отправную точку для собственных соглашений, соответствующих конкретным потребностям.

Чтобы достичь положительных результатов, установите соглашения об именовании и следуйте им при именовании ресурсов для всех своих приложений и организаций. Адаптируйте соглашения по мере развертывания дополнительных приложений и служб на платформе Azure.

## Именование подписок

При именовании подписок подробные имена помогают лучше понять контекст и цель каждого ресурса. Во время работы в среде, которая может содержать несколько подписок, следование общим соглашениям об именовании значительно повышает наглядность.

Вот как выглядит рекомендуемый шаблон именования подписок:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- В большинстве случаев компания будет одинаковой для каждой подписки. Однако в структуре некоторых компаний могут быть дочерние организации. Такими компаниями может управлять центральная ИТ-группа. В этом случае они содержат имя родительской компании (*Contoso*) и дочерней (*North Wind*).

- Отдел — имя для части организации, в которой работает группа сотрудников. Данный элемент необязателен в пространстве имен. Это связано с тем, что некоторым компаниям не требуется вникать в такие подробности из-за их размера.

- Линейка продуктов — конкретное имя для продукта или функции, используемое в отделе. Хотя обычно это имя является необязательным для внутренних служб и приложений, настоятельно рекомендуется использовать его для общедоступных служб, которые будут требовать простого разделения и идентификации (например, для наглядного разделения записей выставления счетов).

- Среда — имя, описывающее такие этапы жизненного цикла развертывания приложений или служб, как разработка, тестирование или производство.

| Компания | Department | Линейка продуктов или служба | Среда | Полное имя |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Производство | Contoso SocialGaming AwesomeService Production |
| Contoso | SocialGaming | AwesomeService | Разработка | Contoso SocialGaming AwesomeService Dev |
| Contoso | IT | InternalApps | Производство | Contoso IT InternalApps Production |
| Contoso | IT | InternalApps | Разработка | Contoso IT InternalApps Dev |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc -->

## Использование аффиксов для избежания неоднозначности

При именовании ресурсов в Azure рекомендуется использовать общепринятые префиксы или суффиксы для идентификации типа и контекста ресурса. Несмотря на то что все сведения о типе, метаданных и контексте можно получить программным путем, максимальное использование общепринятых аффиксов упрощает визуальную идентификацию. При включении аффиксов в соглашения об именовании очень важно четко указать, где будет использоваться аффикс — в начале имени (префикс) или в конце (суффикс).

Например, служба, в которой размещено вычислительное ядро, может иметь такие два имени:

- SvcCalculationEngine (префикс);
- CalculationEngine-Svc (суффикс).

Аффиксы относятся к различным аспектам, описывающим конкретные ресурсы. В следующей таблице приведены примеры типичных случаев.

| Аспект | Пример | Примечания |
| ------ | ------- | ----- |
| Среда | dev, prod, qa | Определяет среду для ресурса |
| Расположение | uw (запад США), ue (восток США) | Определяет регион, в котором развернут ресурс |
| Экземпляр | 01, 02 | Для ресурсов, которые имеют несколько именованных экземпляров (веб-серверы и т. д.) |
| Продукт или служба | свойства | Определяет продукт, приложение или службу, которые поддерживает ресурс |
| Роль | sql, web, messaging | Определяет роль связанного ресурса |

При разработке конкретного соглашения об именовании для компании или проектов важно выбрать общий набор аффиксов, а также их расположение (суффикс или префикс).

## Правила именования и ограничения

К каждому типу ресурса или службы в Azure применяется набор ограничений для имен и областей применения. Все соглашения об именах или шаблоны именования должны отвечать соответствующим правилам именования и области применения. Например, хотя имя виртуальной машины сопоставляется с именем DNS (и поэтому должно быть уникальным в Azure), имя виртуальной сети ограничено группой ресурсов, в которой она создана.

Как правило, следует избегать специальных символов (`-` или `_`) в качестве первого или последнего символа в любом имени, так как их использование нарушает большинство правил проверки.

| Категория | Служба или сущность | Область | Длина | Регистр | Допустимые символы | Рекомендуемый шаблон | Пример |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Группа ресурсов | Группа ресурсов | Глобальные | От 1 до 64 | Без учета регистра | Буквы, цифры, символ подчеркивания и дефис | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Группа ресурсов | Группа доступности | Группа ресурсов | От 1 до 80 | Без учета регистра | Буквы, цифры, символ подчеркивания и дефис | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Общие сведения | Тег | Связанная сущность | 512 (имя), 256 (значение) | Без учета регистра | Буквенно-цифровой | `"key" : "value"` | `"department" : "Central IT"` |
| Среда выполнения приложений | Виртуальная машина | Группа ресурсов | От 1 до 15 | Без учета регистра | Буквы, цифры, символ подчеркивания и дефис | `<name>-<role>-<instance>` | `profx-sql-001` |
| Хранилище | Имя учетной записи хранения (данные о ней) | Глобальные | От 3 до 24 | Нижний регистр | Буквенно-цифровой | `<service short name><type><number>` | `profxdata001` |
| Хранилище | Имя (диски) учетной записи хранения | Глобальные | От 3 до 24 | Нижний регистр | Буквенно-цифровой | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Хранилище | Имя контейнера | Учетная запись хранения | От 3 до 63 |	Нижний регистр | Буквы, цифры и тире | `<context>` | `logs` |
| Хранилище | Имя большого двоичного объекта | Контейнер | От 1 до 1024 | С учетом регистра | Любой символ URL-адреса | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Хранилище | Имя очереди | Учетная запись хранения | От 3 до 63 | Нижний регистр | Буквы, цифры и тире | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Хранилище | Имя таблицы | Учетная запись хранения | От 3 до 63 |Без учета регистра | Буквенно-цифровой | `<service short name>-<context>` | `awesomeservice-logs` |
| Хранилище | Имя файла | Учетная запись хранения | От 3 до 63 | Нижний регистр | Буквенно-цифровой | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Сеть | Виртуальная сеть (VNet) | Группа ресурсов | От 2 до 80 | Без учета регистра | Буквы, цифры, тире, символ подчеркивания и точка | `<service short name>-[section]-vnet` | `profx-vnet` |
| Сеть | Подсеть | Родительская виртуальная сеть | От 2 до 80 | Без учета регистра | Буквы, цифры, символ подчеркивания, тире и точка | `<role>-subnet` | `gateway-subnet` |
| Сеть | Сетевой интерфейс | Группа ресурсов | От 1 до 80 | Без учета регистра | Буквы, цифры, тире, символ подчеркивания и точка | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Сеть | Группа безопасности сети | Группа ресурсов | От 1 до 80 | Без учета регистра | Буквы, цифры, тире, символ подчеркивания и точка | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Сеть | Правило группы безопасности сети | Группа ресурсов | От 1 до 80 | Без учета регистра | Буквы, цифры, тире, символ подчеркивания и точка | `<descriptive context>` | `sql-allow` |
| Сеть | Общедоступный IP-адрес | Группа ресурсов | От 1 до 80 | Без учета регистра | Буквы, цифры, тире, символ подчеркивания и точка | `<vm or service name>-pip` | `profx-sql1-pip` |
| Сеть | Подсистема балансировки нагрузки | Группа ресурсов | От 1 до 80 | Без учета регистра | Буквы, цифры, тире, символ подчеркивания и точка | `<service or role>-lb` | `profx-lb` |
| Сеть | Конфигурация правил балансировки нагрузки | Подсистема балансировки нагрузки | От 1 до 80 | Без учета регистра | Буквы, цифры, тире, символ подчеркивания и точка | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore and period | TODO | TODO
-->

## Упорядочение ресурсов с помощью тегов

Azure Resource Manager поддерживает добавление к сущностям тегов с любыми текстовыми строками, позволяя определить контекст и оптимизировать автоматизацию. Например, такой тег, как `"sqlVersion: "sql2014ee"`, может идентифицировать все виртуальные машины в среде под управлением SQL Server 2014 Enterprise Edition для запуска автоматического сценария с ними. Теги должны использоваться для дополнения и улучшения контекста наряду с выбранным соглашением об именовании.

> [AZURE.TIP] Другим преимуществом тегов является то, что они охватывают несколько групп ресурсов, позволяя связывать и сопоставлять сущности в разных развертываниях.

Каждый ресурс или группа ресурсов может иметь не более **15** тегов. Имя тега ограничено 512 символами, а значение тега — 256 символами.

Дополнительные сведения о добавлении тегов к ресурсам см. в статье [Использование тегов для организации ресурсов Azure](../resource-group-using-tags.md).

Ниже приведены некоторые наиболее распространенные случаи использования тегов.

- **Выставление счетов**. Группировка ресурсов и связывание их с кодами выставления счетов или возврата платежей.
- **Идентификация контекста службы**. Определение групп ресурсов в группах ресурсов для распространенных операций и группировок
- **Контроль доступа и контекст безопасности**. Идентификация роли администратора на основе портфеля, системы, службы, приложения, экземпляра и т. д. 

> [AZURE.TIP] Добавляйте теги заранее — и они будут у вас всегда. Лучше иметь базовую схему добавления тегов заранее и время от времени ее корректировать, чем вносить изменения задним числом.

Примеры некоторых распространенных методов добавления тегов.

| Имя тега | Ключ | Пример | Комментарий |
| -------- | --- | ------- | ------- |
| ИД выставления счета/внутреннего взимания средств за использование | billTo | `IT-Chargeback-1234` | Внутренний ввод/вывод или код выставления счета |
| Оператор или непосредственно ответственное лицо | managedBy | `joe@contoso.com` | Псевдоним или адрес электронной почты |
| Имя проекта | project-name | `myproject` | Имя проекта или линейки продуктов |
| Версия проекта | project-version | `3.4` | Версия проекта или линейки продуктов |
| Среда | environment | `<Production, Staging, QA >` | Идентификатор среды | 
| Уровень | tier | `Front End, Back End, Data` | Идентификация уровня или роли/контекста |
| Профиль данных | dataProfile | `Public, Confidential, Restricted, Internal` | Чувствительность данных, хранящихся в облаке |
 
## Советы и рекомендации

В зависимости от типа приложения некоторые типы ресурсов могут особенно требовательны к именованию и соглашениям. Дополнительные сведения и контекст для них приведены ниже.

### Виртуальные машины

Особенно в более крупных топологиях внимательное именование виртуальных машин существенно оптимизирует идентификацию роли и цели каждой машины, а также поддержку более прогнозированного сценария.

> [AZURE.WARNING] Обратите внимание, что все виртуальные машины в Azure имеют имя ресурса Azure и имя узла операционной системы. Если имя ресурса и имя узла не совпадают, управление виртуальными машинами может усложниться (например, если виртуальная машина создается из VHD-файла, который уже содержит настроенную операционную систему с именем узла), так что этого нужно избегать.

- [Соглашения об именовании виртуальных машин Windows Server](https://support.microsoft.com/ru-RU/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

###	Учетные записи хранения и сущности хранилища

Существует два основных варианта использования учетных записей хранения: резервное копирование дисков для виртуальных машин и хранение данных в больших двоичных объектах, очередях и таблицах. Учетные записи хранения, используемые для дисков виртуальных машин, должны отвечать соглашению об именовании по связыванию их с именем родительской виртуальной машины. На случай необходимости применять несколько учетных записей хранения для номеров SKU современных виртуальных машин учетные записи хранения должны содержать числовой суффикс.

> [AZURE.TIP] Учетные записи хранения — для данных или дисков — должны следовать соглашению об именовании, позволяющему использовать несколько записей хранения (то есть всегда должны содержать числовой суффикс).

Можно настроить пользовательское доменное имя для доступа к данным больших двоичных объектов в вашей учетной записи хранения Azure. Конечной точкой по умолчанию для службы BLOB-объектов является `https://mystorage.blob.core.windows.net`.

Однако после сопоставления личного домена (например, www.contoso.com) с конечной точкой больших двоичных объектов для вашей учетной записи хранения вы также сможете, используя этот домен, получать доступ к данным больших двоичных объектов в учетной записи хранения. Например, с помощью пользовательского доменного имени можно осуществлять доступ к `http://mystorage.blob.core.windows.net/mycontainer/myblob` как к `http://www.contoso.com/mycontainer/myblob`.

Дополнительные сведения о настройке этой функции см. в статье [Настройка пользовательского доменного имени для конечной точки хранилища BLOB-объектов](../storage/storage-custom-domain-name.md).

Дополнительные сведения об именовании больших двоичных объектов, контейнеров и таблиц см. в следующих статьях.

- [Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них](https://msdn.microsoft.com/library/dd135715.aspx)
- [Именование очередей и метаданных](https://msdn.microsoft.com/library/dd179349.aspx)
- [Именование таблиц](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Имя большого двоичного объекта может содержать любое сочетание символов, но символы зарезервированного URL-адреса необходимо правильно изолировать. Избегайте имен объектов, которые заканчиваются точкой (.), косой чертой (/) либо последовательностью или сочетанием этих двух символов. По соглашению, косая черта является **виртуальным** разделителем каталогов. Не используйте в имени большого двоичного объекта обратную косую черту (\\). Клиентские API могут разрешать ее использование, но затем произойдет сбой хэширования и подписи не будут совпадать.

Нельзя изменить имя учетной записи хранения или контейнера после ее создания. Если вы хотите использовать новое имя, необходимо удалить старую учетную запись и создать новую.

> [AZURE.TIP] Прежде чем начинать разработку новой службы или приложения, рекомендуется установить соглашение об именовании для всех учетных записей и типов хранения.

## Пример — развертывание N-уровневой службы

В этом примере мы определим конфигурацию N-уровневой службы, состоящей из внешних серверов IIS (размещенных на виртуальных машинах Windows Server), с помощью SQL Server (размещенного на двух виртуальных машинах Windows Server), кластера ElasticSearch (размещенного на 6 виртуальных машинах Linux) и связанных учетных записей хранения, виртуальных сетей, групп ресурсов и балансировщиков нагрузки.

Для начала определим контекстные соглашения для этого приложения.

| Сущность | Соглашение | Описание |
| ------ | ---------- | ------------ |  
| Имя службы | `profx` | Короткое имя приложения или службы, для которых выполняется развертывание |
| Среда | `prod` | Это необходимо для развертывания в рабочей среде (в отличие от среды контроля качества, тестирования и т. д.) |

На основе этого базового плана можно затем наметить соглашения для всех типов ресурсов.

| Тип ресурса | Базовое соглашение | Пример |
| ------------- | --------------- | ------- |
| Подписка | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Группа ресурсов | `servicename-rg` | `profx-rg` |
| Виртуальная сеть | `servicename-vnet` | `profx-vnet` |
| Подсеть | `role-subnet` | `sql-vnet` |
| Подсистема балансировки нагрузки | `servicename-lb` | `profx-lb` |
| Виртуальная машина | `servicename-role[number]` | `profx-sql0` |
| Учетная запись хранения | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Как показано на схеме ниже:

![схема топологии приложения](media/guidance-naming-conventions/guidance-naming-convention-example.png "Топология примера приложения")

## Пример — сценарий CLI Azure для развертывания, описанного в предыдущем примере

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

	# Create the network interface card for this VM
	azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

	# Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
	azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
		--boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
	create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```

<!---HONumber=AcomDC_0302_2016-->