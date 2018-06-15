---
title: 'Краткое руководство: запуск рабочего процесса в службе Microsoft Genomics | Документация Майкрософт'
description: В этом кратком руководстве объясняется, как загрузить входные данные в хранилище BLOB-объектов и запустить рабочий процесс в службе Microsoft Genomics.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: 1436ad54eb13052aa87ccfd5adc371c8d7d5a100
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403796"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Краткое руководство: запуск рабочего процесса в службе Microsoft Genomics

Microsoft Genomics — это масштабируемая надежная служба вторичного анализа, которая может быстро обрабатывать геном, начиная с простых операций чтения до выполнения согласованного считывания и вызовов вариантов. Начните работу, выполнив несколько шагов: 
1.  Настройка. Создайте учетную запись Microsoft Genomics на портале Azure и установите клиент Python Microsoft Genomics в локальной среде. 
2.  Передача входных данных. Создайте учетную запись хранения Microsoft Azure на портале Azure и отправьте входные файлы. Входные файлы должны быть парными файлами для чтения (FASTQ или BAM).
3.  Запуск. Запустите рабочие процессы в службе Microsoft Genomics с помощью интерфейса командной строки Microsoft Genomics. 

Дополнительные сведения о Microsoft Genomics см. в [этой статье](overview-what-is-genomics.md).

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Настройка. Создание учетной записи Microsoft Genomics на портале Azure

Чтобы создать учетную запись Microsoft Genomics, перейдите на [портал Azure](https://portal.azure.com/#create/Microsoft.Genomics). Если у вас нет подписки Azure, создайте ее, прежде чем создавать учетную запись Microsoft Genomics. 

![Microsoft Genomics на портале Azure](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics on Azure portal")



Настройте учетную запись Genomics, указав следующую информацию, как показано на предыдущем рисунке. 

 |**Параметр**          |  **Рекомендуемое значение**  | **Описание поля** |
 |:-------------       |:-------------         |:----------            |
 |Имя учетной записи         | MyGenomicsAccount     |Выберите уникальный идентификатор учетной записи. Допустимые имена см. в статье [Соглашения об именовании](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
 |Подписка         | Имя вашей подписки|Это единица выставления счетов для служб Azure. Дополнительные сведения см. на странице [подписок](https://account.azure.com/Subscriptions) |      
 |Группа ресурсов       | MyResourceGroup       |  Группы ресурсов позволяют объединить несколько ресурсов Azure (учетная запись хранения, учетная запись Genomics и т. д.) в одну группу для простоты управления. Дополнительные сведения см. в разделе [Группы ресурсов] (https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Допустимые имена групп ресурсов см. в статье [Соглашения об именовании](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
 |Расположение                   | Западный регион США 2                    |    Служба доступна в таких регионах: западная часть США 2, Западная Европа и Юго-Восточная Азия. |




В строке меню вверху выберите пункт "Уведомления", чтобы отслеживать процесс развертывания.
![Уведомления Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box.png "Microsoft Genomics Notifications")



## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Настройка. Установка клиента Microsoft Genomics Python

Пользователи должны установить в своей локальной среде два клиента: Python и Microsoft Genomics Python. 

### <a name="install-python"></a>Установка Python

Клиент Microsoft Genomics Python совместим с Python 2.7. Рекомендуем использовать версию 2.7.12 или более позднюю, например 2.7.14. Файл установки можно скачать [здесь](https://www.python.org/downloads/). 


### <a name="install-the-microsoft-genomics-client"></a>Установка клиента Microsoft Genomics

Для установки клиента Microsoft Genomics `msgen` используйте pip для Python. В следующих инструкциях предполагается, что Python уже есть в системном пути. Если не удается распознать установку pip, добавьте Python и вложенную папку скриптов в системный путь.


```
pip install --upgrade --no-deps msgen
pip install msgen
```


Если вы не хотите устанавливать `msgen` как двоичный файл во всей системе и изменять пакеты Python во всей системе, используйте флаг `–-user` с `pip`.
Если вы выполняете установку с помощью пакетов или файла setup.py, устанавливаются все необходимые пакеты. Если вы выполняете установку иначе, базовые необходимые пакеты для msgen можно скачать по этим ссылкам: 

 * [Azure-storage](https://pypi.python.org/pypi/azure-storage); 
 * [Requests](https://pypi.python.org/pypi/requests). 


Эти пакеты также можно установить с помощью `pip`, `easy_install` или стандартной процедуры `setup.py`. 





### <a name="test-the-microsoft-genomics-client"></a>Тестирование клиента Microsoft Genomics
Чтобы протестировать клиент Microsoft Genomics, скачайте файл конфигурации из своей учетной записи Genomics. Чтобы перейти к учетной записи Genomics, выберите пункт **Все службы** в левом верхнем углу, отфильтруйте службы и выберите учетную запись Genomics.


![Фильтр для Microsoft Genomics на портале Azure](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Filter for Microsoft Genomics on Azure portal")



Выберите учетную запись Genomics, которую вы только что создали, откройте раздел **Ключи доступа** и скачайте файл конфигурации.

![Скачивание файла конфигурации из Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Download config file from Microsoft Genomics")


Протестируйте работу клиента Microsoft Genomics Python с помощью следующей команды:


```
msgen list -f “<full path where you saved the config file>”
```

## <a name="create-a-microsoft-azure-storage-account"></a>Создание учетной записи хранения Microsoft Azure 
Служба Microsoft Genomics ожидает входные файлы в виде блочных BLOB-объектов в учетной записи хранения Azure. Она также записывает выходные файлы как блочные BLOB-объекты в определенный пользователем контейнер в учетной записи хранения Azure. Входные и выходные файлы могут находиться в разных учетных записях хранения.
Если в вашей учетной записи хранения Azure уже есть данные, вам нужно только убедиться, что она находится в том же расположении, что и учетная запись Genomics. Иначе при запуске службы Genomics вы будете оплачивать исходящие данные. Если у вас еще нет учетной записи хранения Microsoft Azure, необходимо создать ее и отправить туда данные. Дополнительные сведения об учетных записях хранения Azure см. [здесь](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account). Из этой статьи вы узнаете, что такое учетная запись хранения и для чего она нужна. Чтобы создать учетную запись хранения Microsoft Azure, перейдите на [портал Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM ).  

![Колонка создания учетной записи хранения](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade.png "Storage account create blade")

Настройте учетную запись хранения, указав следующую информацию, как показано на предыдущем рисунке. Используйте стандартные параметры для учетной записи хранения. Укажите только, что она не является учетной записью общего назначения, а предназначена для хранилища BLOB-объектов. В хранилище BLOB-объектов операции скачивания или отправки могут выполняться в 2–5 раз быстрее. 


 |**Параметр**          |  **Рекомендуемое значение**  | **Описание поля** |
 |:-------------------------       |:-------------         |:----------            |
 |ИМЯ         | MyStorageAccount     |Выберите уникальный идентификатор учетной записи. Допустимые имена см. в статье [Соглашения об именовании](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
 |Модель развертывания         | Диспетчер ресурсов| Resource Manager является рекомендуемой моделью развертывания. Дополнительные сведения см. в статье [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model). |      
 |Тип учетной записи       | Хранилище BLOB-объектов       |  В хранилище BLOB-объектов операции скачивания или отправки могут выполняться в 2–5 раз быстрее, чем в хранилище общего назначения. |
 |Производительность                  | Стандартная                   | По умолчанию используется уровень "Стандартный". Дополнительные сведения об учетных записях хранения уровня "Стандартный" и "Премиум" см. в статье [Введение в хранилище Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).    |
 |Репликация                  | Локально избыточное хранилище                  | Локально избыточное хранилище реплицирует ваши данные в центр данных в регионе, в котором создана учетная запись хранения. Дополнительные сведения см. в статье о [репликации службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).    |
 |Требуется безопасная передача данных                  | Отключено                 | По умолчанию — отключен. Дополнительные сведения о безопасности передачи данных см. в статье о [требовании безопасной передачи](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer).    |
 |Уровень доступа                  | Горячий                   | Горячий уровень доступа означает, что доступ к объектам в учетной записи хранения будет осуществляться часто.    |
 |Подписка         | Ваша подписка Azure. |Дополнительные сведения о подписках см. [здесь](https://account.azure.com/Subscriptions). |      
 |Группа ресурсов       | MyResourceGroup       |  Можно выбрать группу ресурсов, к которой относится учетная запись Genomics. Допустимые имена групп ресурсов см. в статье [Соглашения об именовании](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
 |Расположение                  | Западный регион США 2                  | Используйте то же расположение, что и для учетной записи Genomics, для сокращения расходов на исходящие данные и уменьшения задержки при передаче данных. Служба Genomics доступна в таких регионах: западная часть США 2, Западная Европа и Юго-Восточная Азия.    |
 |виртуальные сети;                | Отключено                   | По умолчанию — отключен. Дополнительные сведения см. в статье о [виртуальных сетях Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).    |





Нажмите кнопку "Создать", чтобы создать учетную запись хранения. Как и во время создания учетной записи Genomics, вы можете выбрать значок уведомлений в строке меню вверху, чтобы отслеживать процесс развертывания. 


## <a name="upload-input-data-to-your-storage-account"></a>Передача входных данных в учетную запись хранения

Служба Microsoft Genomics ожидает в качестве входных файлов парные файлы для чтения. Вы можете отправить собственные данные или использовать общедоступные примеры данных. Если вы хотите использовать общедоступные примеры данных, вы найдете их по ссылкам ниже:


[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)


Создайте в своей учетной записи хранения один контейнер больших двоичных объектов для входных данных и еще один — для выходных данных.  Передайте входные данные в контейнер больших двоичных объектов. Для этого можно использовать различные инструменты, например [Обозреватель службы хранилища Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/), [blobporter](https://github.com/Azure/blobporter) или [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 



## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Запуск рабочего процесса в службе Microsoft Genomics с помощью Python 

Для запуска рабочего процесса в службе Microsoft Genomics измените файл config.txt, указав в нем контейнер хранилища входных и выходных данных.
Откройте файл config.txt, скачанный из учетной записи Genomics. Разделы, которые необходимо указать: ключ подписки и шесть элементов в конце файла (имя учетной записи хранения, ключ и имя контейнера для входных данных, имя учетной записи хранения, ключ и имя контейнера для выходных данных). Чтобы найти эти сведения, перейдите на портале в раздел **Ключи доступа** вашей учетной записи хранения или непосредственно в обозреватель службы хранилища Azure.  


![Конфигурация Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomics config")

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Отправка рабочего процесса в клиент Microsoft Genomics службы Microsoft Genomics

Используйте клиент Microsoft Genomics Python для отправки рабочего процесса с помощью следующей команды:


```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```


Чтобы просмотреть состояние рабочего процесса, используйте следующую команду: 
```python
msgen list -f c:\temp\config.txt 
```


После выполнения рабочего процесса вы можете просмотреть выходные файлы в контейнере выходных данных своей учетной записи хранения Azure. 


## <a name="next-steps"></a>Дополнительная информация
С помощью инструкций из этой статьи вы научились отправлять примеры входных данных в службу хранилища Azure и отправлять рабочий процесс в службу Microsoft Genomics через клиент Python `msgen`. Дополнительные сведения о других типах входных файлов, которые могут использоваться в службе Microsoft Genomics, см. в статьях об отправке [парных FASTQ-файлов](quickstart-input-pair-FASTQ.md) | [BAM-файлов](quickstart-input-BAM.md) | [нескольких FASTQ- или BAM-файлов](quickstart-input-multiple.md). Кроме того, вы можете выполнить задачи в этом руководстве с помощью нашего [руководства службы "Записные книжки Azure"](http://aka.ms/genomicsnotebook).