---
title: Переменные среды вычислительного узла пакетной службы Azure | Документы Майкрософт
description: Справочник по переменным среды вычислительного узла для пакетной аналитики Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/05/2017
ms.author: danlep
ms.openlocfilehash: ca8d6a6484cd1f145e7d807681bf2d012f2399e0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312707"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Переменные среды вычислительного узла пакетной службы Azure
[Пакетная служба Azure](https://azure.microsoft.com/services/batch/) задает на вычислительных узлах указанные ниже переменные среды. Вы можете ссылаться на них в командных строках задач, а также в программах и сценариях, запускаемых этими командными строками.

Дополнительные сведения об использовании переменных среды в пакетной службе см. в статье [Параметры среды для задач](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Видимость переменных среды

Эти переменные среды видимы только в контексте **пользователя задачи** — учетной записи пользователя на узле, с помощью которой выполняется задача. Вы *не* увидите их, если [удаленно подключитесь](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) к вычислительному узлу через протокол удаленного рабочего стола (RDP) или Secure Shell (SSH) и отобразите список переменных среды. Это вызвано тем, что учетная запись, используемая для удаленного подключения, отличается от учетной записи, используемой задачей.

## <a name="command-line-expansion-of-environment-variables"></a>Расширение переменных среды в командной строке

Командные строки, запускаемые задачами на вычислительных узлах, не выполняются в оболочке. Поэтому они не могут автоматически использовать функции оболочки, например расширение переменных среды (в том числе `PATH`). Чтобы воспользоваться этими функциями, нужно **вызвать оболочку** в командной строке. Например, запустите `cmd.exe` на вычислительных узлах Windows или `/bin/sh` на узлах Linux:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Переменные среды

| Имя переменной                     | ОПИСАНИЕ                                                              | Доступность | Пример |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Имя учетной записи пакетной службы, к которой относится задача.                  | Все задачи.   | mybatchaccount |
| AZ_BATCH_CERTIFICATES_DIR       | Каталог в [рабочем каталоге задачи][files_dirs], где хранятся сертификаты для вычислительных узлов Linux. Обратите внимание, что эта переменная среды не применяется к вычислительным узлам Windows.                                                  | Все задачи.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_JOB_ID                 | Идентификатор задания, к которому относится задача | Все задачи, кроме задачи запуска. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Полный путь к [каталогу задачи][files_dirs] подготовки задания на узле. | Все задачи, кроме задачи запуска и задачи подготовки задания. Доступна, только если для задания настроена задача подготовки задания. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Полный путь к [рабочему каталогу задачи][files_dirs] подготовки задания на узле. | Все задачи, кроме задачи запуска и задачи подготовки задания. Доступна, только если для задания настроена задача подготовки задания. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_NODE_ID                | Идентификатор узла, которому назначена задача. | Все задачи. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_ROOT_DIR          | Полный путь к корневому каталогу всех [каталогов пакетной службы][files_dirs] на узле. | Все задачи. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Полный путь к [общему каталогу][files_dirs] на узле. Все задачи, которые выполняются на узле, имеют доступ на чтение и запись для этого каталога. У задач, выполняемых на других узлах, нет удаленного доступа к этому каталогу (это не "общий" сетевой каталог). | Все задачи. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Полный путь к [каталогу запуска задачи][files_dirs] на узле. | Все задачи. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Идентификатор пула, в котором выполняется задача | Все задачи. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Полный путь к [каталогу задачи][files_dirs] на узле. Этот каталог содержит `stdout.txt` и `stderr.txt` для задачи, а также AZ_BATCH_TASK_WORKING_DIR. | Все задачи. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Идентификатор текущей задачи. | Все задачи, кроме задачи запуска. | task001 |
| AZ_BATCH_TASK_WORKING_DIR       | Полный путь к [рабочему каталогу задачи][files_dirs] на узле. Выполняемая задача имеет доступ на чтение и запись для этого каталога. | Все задачи. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Список узлов и число ядер на узел, выделяемых для [задачи с несколькими экземплярами][multi_instance]. Узлы и ядра указаны в формате `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, где за числом узлов следует один или несколько IP-адресов узла, а также число ядер для каждого узла. |  Основные задачи и подзадачи с несколькими экземплярами. |`2 10.0.0.4 1 10.0.0.5 1` |
| AZ_BATCH_NODE_LIST              | Список узлов, выделяемых для [задачи с несколькими экземплярами][multi_instance], в формате `nodeIP;nodeIP`. | Основные задачи и подзадачи с несколькими экземплярами. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_HOST_LIST              | Список узлов, выделяемых для [задачи с несколькими экземплярами][multi_instance], в формате `nodeIP,nodeIP`. | Основные задачи и подзадачи с несколькими экземплярами. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_MASTER_NODE            | IP-адрес и порт вычислительного узла, где выполняется основная задача для [задачи с несколькими экземплярами][multi_instance]. | Основные задачи и подзадачи с несколькими экземплярами. | `10.0.0.4:6000`|
| AZ_BATCH_TASK_SHARED_DIR | Путь к каталогу, идентичный для основной задачи и всех подзадач [задачи с несколькими экземплярами][multi_instance]. Он существует на каждом узле, где выполняется задача с несколькими экземплярами, и доступен для чтения и записи командам, выполняющимся на этом узле (как [команде координации][coord_cmd], так и [команде приложения][app_cmd]). У подзадач или основной задачи, выполняемых на других узлах, нет удаленного доступа к этому каталогу (это не "общий" сетевой каталог). | Основные задачи и подзадачи с несколькими экземплярами. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Указывает, является ли текущий узел главным для [задачи с несколькими экземплярами][multi_instance]. Возможные значения: `true` и `false`.| Основные задачи и подзадачи с несколькими экземплярами. | `true` |
| AZ_BATCH_NODE_IS_DEDICATED | Если имеет значение `true`, то текущий узел является выделенным. Если имеет значение `false`, то это [низкоприоритетный узел](batch-low-pri-vms.md). | Все задачи. | `true` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
