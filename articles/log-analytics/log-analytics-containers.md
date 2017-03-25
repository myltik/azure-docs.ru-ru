---
title: "Решение &quot;Контейнеры&quot; в Azure Log Analytics | Документация Майкрософт"
description: "Решение &quot;Контейнеры&quot; в Log Analytics позволяет централизованно просматривать узлы контейнеров Docker и Windows, а также управлять ими."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 45b07f5dd29b7e920ac2abe6765020e0337ecf95
ms.lasthandoff: 03/11/2017


---
# <a name="containers-preview-solution-log-analytics"></a>Решение "Контейнеры" (предварительная версия) в Log Analytics
В этой статье описано, как настроить и использовать решение "Контейнеры" в Log Analytics для централизованного просмотра узлов контейнеров Docker и Windows, а также управление ими. Docker — это система виртуализации программного обеспечения, с помощью которой можно создавать контейнеры, автоматизирующие развертывание программного обеспечения в соответствующей ИТ-инфраструктуре.

Благодаря этому решению можно видеть, какие контейнеры работают на узлах контейнеров и какие образы выполняются в контейнерах. Также можно просматривать подробные сведения аудита, в том числе команды, используемые в контейнерах. Кроме того, вы можете устранять неполадки контейнеров, просматривая централизованные журналы и выполняя в них поиск, без необходимости удаленного просмотра узлов Docker или Windows. Решение позволяет находить контейнеры, содержащие ошибки и использующие слишком много ресурсов на узле. Также у вас есть возможность централизованно просматривать сведения об использовании и производительности ЦП, памяти, хранилища и сети по контейнерам. На компьютерах под управлением Windows можно централизовать и сравнивать журналы из Windows Server, Hyper-V и контейнеров Docker.

На схеме ниже показаны связи между разными узлами контейнера и агентами с OMS.

![Схема контейнеров](./media/log-analytics-containers/containers-diagram.png)

## <a name="installing-and-configuring-the-solution"></a>Установка и настройка решения
Для установки и настройки решений используйте указанные ниже данные.

Решение для контейнеров необходимо добавить в рабочую область OMS из [Azure Мarketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) или в соответствии с инструкциями по [добавлению решений Log Analytics из коллекции решений](log-analytics-add-solutions.md).

Docker с OMS можно установить и использовать несколькими способами:

* В поддерживаемых операционных системах Linux установите и запустите Docker, а затем установите и настройте агент OMS для Linux.
* В CoreOS невозможно запустить агент OMS для Linux. Вместо этого можно запустить контейнерную версию агента OMS для Linux.
* В Windows Server 2016 и Windows 10 установите модуль и клиент Docker, после чего подключите агент, чтобы собрать сведения и отправить их в Log Analytics.


Вы можете ознакомиться с поддерживаемыми версиями Docker и операционными системами Linux для узла контейнера на портале [GitHub](https://github.com/Microsoft/OMS-docker).

Дополнительные сведения о том, как установить и настроить модули Docker на компьютерах под управлением Windows, см. в [этой статье](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

> [!IMPORTANT]
> Docker необходимо запустить **перед** установкой [агента OMS для Linux](log-analytics-linux-agents.md) на узлах контейнера. Если вы уже установили агент перед установкой Docker, необходимо переустановить агент OMS для Linux. Дополнительные сведения о Docker см. на [веб-сайте Docker](https://www.docker.com).
>
>

Для мониторинга контейнеров необходимо настроить указанные ниже параметры на узлах контейнера.

## <a name="configure-settings-for-a-linux-container-host"></a>Настройка параметров для узла контейнера Linux

В качестве узлов контейнера поддерживаются следующие дистрибутивы Linux (x64):

- Ubuntu 14.04 LTS, 16.04 LTS;
- CoreOS (стабильный выпуск);
- Amazon Linux 2016.09.0;
- openSUSE 13.2
- CentOS 7
- SLES 12
- RHEL 7.2;

После установки Docker используйте приведенные ниже параметры узла контейнера, чтобы настроить агент для использования с Docker. Вам потребуется [идентификатор и ключ рабочей области OMS](log-analytics-linux-agents.md).


### <a name="for-all-linux-container-hosts-except-coreos"></a>Для всех узлов контейнера Linux, за исключением CoreOS

- Дополнительные указания см. в разделе [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) (Инструкции по установке агента OMS для Linux).

### <a name="for-all-linux-container-hosts-including-coreos"></a>Для всех узлов контейнера Linux, включая CoreOS

Запустите контейнер OMS, который вы хотите отслеживать. Используйте следующий пример, внеся в него необходимые изменения:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>Переход от использования установленного агента Linux к использованию агента в контейнере
Если ранее вы использовали установленный напрямую агент и теперь вместо него хотите использовать агент, работающий в контейнере, необходимо сначала удалить агент OMS. См. раздел [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) (Шаги по установке агента OMS для Linux).

## <a name="supported-windows-versions"></a>Поддерживаемые версии Windows

- Windows Server 2016
- Юбилейный выпуск Windows 10 (профессиональный или корпоративный)

### <a name="docker-versions-supported-on-windows"></a>Версии Docker, поддерживаемые в Windows

- Docker 1.12–1.13

### <a name="preparation-before-installing-agents"></a>Подготовка к установке агентов

Настройте службу Docker, прежде чем устанавливать агенты на компьютерах под управлением Windows. Конфигурация позволяет агенту Windows или расширению виртуальной машины Log Analytics использовать TCP-сокет Docker, чтобы агенты могли получить удаленный доступ к управляющей программе Docker и возможность получать данные мониторинга.

Компьютеры под управлением Windows не поддерживают данные о производительности.

Дополнительные сведения о настройке управляющей программы Docker под управлением Windows см. в статье [Docker Engine on Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) (Модуль Docker в Windows).

#### <a name="to-start-docker-and-verify-its-configuration"></a>Запуск Docker и проверка его конфигурации

1.    Включите канал TCP и именованный канал в Windows PowerShell.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd -H npipe:// -H 0.0.0.0:2375 --register-service
    Start-Service docker
    ```

2.    Проверьте конфигурацию, выполнив команду netstat. Должен отобразиться порт 2375.

    ```
    PS C:\Users\User1> netstat -a | sls 2375

    TCP    127.0.0.1:2375         Win2016TP5:0           LISTENING
    TCP    127.0.0.1:2375         Win2016TP5:49705       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49706       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49707       ESTABLISHED
    TCP    127.0.0.1:2375         Win2016TP5:49708       ESTABLISHED
    TCP    127.0.0.1:49705        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49706        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49707        Win2016TP5:2375        ESTABLISHED
    TCP    127.0.0.1:49708        Win2016TP5:2375        ESTABLISHED
    ```

### <a name="install-windows-agents"></a>Установка агентов Windows

Чтобы включить мониторинг контейнеров Windows и Hyper-V, установите агенты на компьютерах Windows, которые являются узлами контейнера. Сведения для компьютеров под управлением Windows в локальной среде см. в статье [Подключение компьютеров Windows к Log Analytics](log-analytics-windows-agents.md). Виртуальные машины, запущенные в Azure, следует подключить к Log Analytics с помощью [расширения виртуальной машины](log-analytics-azure-vm-extension.md).

Чтобы убедиться, что решение "Контейнеры" настроено правильно, сделайте следующее:

- Проверьте, был ли пакет управления скачан должным образом, и найдите файл *ContainerManagement.xxx*.
    - Файлы должны находиться в папке, расположенной по адресу C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs.
- Проверьте правильность идентификатора рабочей области OMS, выбрав **Панель управления** > **Система и безопасность**.
    - Откройте **Microsoft Monitoring Agent** и убедитесь, что сведения о рабочей области правильны.


## <a name="containers-data-collection-details"></a>Сведения о сборе данных из контейнеров
Решение "Контейнеры" собирает различные метрики производительности и данные журналов из узлов контейнеров и контейнеров, в которых включен агент.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для решения "Контейнеры".

| платформа | [Агент OMS для Linux](log-analytics-linux-agents.md) | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Да](./media/log-analytics-containers/oms-bullet-green.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |Каждые 3 минуты |

| платформа | [Агент Windows](log-analytics-windows-agents.md) | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Да](./media/log-analytics-containers/oms-bullet-green.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |Каждые 3 минуты |

| платформа | [Расширение виртуальной машины Log Analytics](log-analytics-azure-vm-extension.md) | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Таблицы Azure |![Да](./media/log-analytics-containers/oms-bullet-green.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |Каждые 3 минуты |

В таблице ниже приведены примеры типов данных, собранных решением "Контейнеры", и типов данных, которые используются при поиске в журналах и в результатах. Тем не менее компьютеры под управлением Windows еще не поддерживают данные о производительности.

| Тип данных | Тип данных, используемый для поиска в журналах | Поля |
| --- | --- | --- |
| Производительность узлов и контейнеров | `Type=Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Список контейнеров | `Type=ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Список образов контейнеров | `Type=ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Журнал контейнеров | `Type=ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Журнал службы контейнеров | `Type=ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Мониторинг контейнеров
Включив решение на портале OMS, вы увидите плитку **Контейнеры**, на которой отображаются сводные сведения об узлах контейнеров и контейнерах, запущенных на узлах.

![Плитка "Контейнеры"](./media/log-analytics-containers/containers-title.png)

Плитка показывает число контейнеров в среде, число запущенных и остановленных контейнеров, а также число контейнеров, работа которых завершилась сбоем.

### <a name="using-the-containers-dashboard"></a>Использование панели мониторинга "Контейнеры"
Щелкните плитку **Контейнеры**. Отсюда можно открыть представления, упорядоченные по:

* событиям контейнера;
* Ошибки
* состоянию контейнера;
* списку образов контейнеров;
* производительности ЦП и памяти.

Каждая область на панели мониторинга — это визуальное представление поиска по собранным данным.

![Панель мониторинга "Контейнеры"](./media/log-analytics-containers/containers-dash01.png)

![Панель мониторинга "Контейнеры"](./media/log-analytics-containers/containers-dash02.png)

Перейдите в колонку **состояния контейнера** и щелкните верхнюю область, как показано ниже.

![Состояние контейнера](./media/log-analytics-containers/containers-status.png)

Откроется колонка "Поиск по журналам" со сведениями об узлах и работающих в них контейнерах.

![Колонка "Поиск по журналам" для контейнеров](./media/log-analytics-containers/containers-log-search.png)

Здесь можно изменить поисковый запрос таким образом, чтобы найти нужную вам информацию. Дополнительные сведения об использовании поиска по журналам см. в статье [Поиск по журналам в Log Analytics](log-analytics-log-searches.md).

Например, поисковый запрос можно изменить так, чтобы отобразить все остановленные контейнеры вместо работающих. Для этого в поисковом запросе следует изменить условие **Выполняется** на **Остановлено**.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Устранение неполадок с помощью поиска контейнера со сбоем
OMS добавляет к контейнеру пометку **Сбой**, если такой контейнер завершил работу с ненулевым кодом выхода. Обзор ошибок и сбоев в среде можно просмотреть в колонке **Контейнеры со сбоями**.

### <a name="to-find-failed-containers"></a>Поиск контейнеров со сбоями
1. Щелкните колонку **Container Events** (События контейнеров).  
   Колонка ![Container Events](./media/log-analytics-containers/containers-events.png) (События контейнеров)
2. Откроется колонка "Поиск по журналам", в которой отобразится состояние контейнеров, как показано ниже.  
   ![Состояние контейнеров](./media/log-analytics-containers/containers-container-state.png)
3. Затем щелкните число контейнеров со сбоем, чтобы просмотреть дополнительные сведения, включая размер образа и число образов после остановки и сбоя. Разверните пункт **Показать больше**, чтобы просмотреть идентификатор образа.  
   ![Контейнеры со сбоями](./media/log-analytics-containers/containers-state-failed.png)
4. Найдите контейнер, в котором выполняется этот образ. Введите в поисковый запрос код
   `Type=ContainerInventory <ImageID>` для отображения журналов. Можно прокрутить страницу, чтобы просмотреть контейнер со сбоем.  
   ![Контейнеры со сбоями](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Поиск данных контейнера по журналам
При устранении определенной ошибки эта функция помогает узнать, где именно в среде возникает эта ошибка. С помощью журналов следующих типов можно создавать запросы для получения нужных вам сведений.

* **ContainerInventory** — этот тип используется для получения сведений о расположении контейнеров, их именах и образах, которые в них выполняются.
* **ContainerImageInventory** — этот тип используется для поиска сведений, упорядоченных по образам, и для просмотра информации об образах, включая идентификаторы или размеры образов.
* **ContainerLog** — этот тип используется, если требуется найти определенные сведения или записи в журнале ошибок.
* **ContainerServiceLog** — этот тип используется для поиска в журнале аудита данных об управляющей программе Docker, включая команды для запуска, остановки и извлечения.

### <a name="to-search-logs-for-container-data"></a>Поиск данных контейнера по журналам
* Выберите образ, в котором недавно произошел сбой, и найдите журнал ошибок для этого образа. Сначала найдите имя контейнера, в котором выполняется этот образ, выполнив поиск в журнале **ContainerInventory**. Например, выполните поиск по запросу `Type=ContainerInventory ubuntu Failed`  
    ![Поиск контейнеров Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Запишите имя контейнера в строке **Имя** и выполните поиск по журналам, указав это имя. В нашем примере поисковый запрос будет выглядеть так: `Type=ContainerLog adoring_meitner`.

**Просмотр сведений о производительности**

Начиная создавать запрос, прежде всего следует узнать, какие данные можно найти с его помощью. Например, чтобы просмотреть все данные о производительности, попробуйте использовать общий запрос следующего вида:

```
Type=Perf
```

![Производительность контейнеров](./media/log-analytics-containers/containers-perf01.png)

Более наглядно результаты можно отобразить, щелкнув слово **Метрики**.

![Производительность контейнеров](./media/log-analytics-containers/containers-perf02.png)

Чтобы отобразить данные производительности только для конкретного контейнера, введите его имя в правой части запроса.

```
Type=Perf <containerName>
```

Вы увидите список метрик производительности, собранных для отдельного контейнера.

![Производительность контейнеров](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Примеры запросов для поиска по журналам
При создании запросов часто бывает полезно начать с одного-двух примеров, внося затем в них изменения в соответствии с конкретной средой. Сначала можно поэкспериментировать с колонкой **Заметные запросы**, чтобы научиться создавать более сложные запросы.

![Запросы по контейнерам](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Сохранение запросов для поиска по журналам
Сохранение запросов — это стандартная функция в Log Analytics. Она позволяет сохранять полезные запросы для использования в будущем.

Создав запрос, который вы считаете полезным, сохраните его, щелкнув **Избранное** в верхней части страницы поиска по журналам. Позднее вы сможете легко открыть его на странице **Моя панель мониторинга**.

## <a name="next-steps"></a>Дальнейшие действия
* [Выполните поиск по журналам](log-analytics-log-searches.md), чтобы просмотреть подробные записи с данными контейнеров.

