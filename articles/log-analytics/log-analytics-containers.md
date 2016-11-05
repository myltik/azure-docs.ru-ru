---
title: 'Решение '
контейнеры": ''
в: ''
log: ''
analytics: ''
'|': ''
microsoft: ''
azure": ''
description: 'Решение '
позволяет: ''
централизованно: ''
просматривать: ''
узлы: ''
контейнера: ''
docker: ''
и: ''
управлять: ''
ими.": ''
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="containers-(preview)-solution-log-analytics"></a>Решение "Контейнеры" (предварительная версия) в Log Analytics
В этой статье описано, как настроить и использовать решение "Контейнеры" в Log Analytics для централизованного просмотра узлов контейнеров Docker и управления ими. Docker — это система виртуализации программного обеспечения, с помощью которой можно создавать контейнеры, автоматизирующие развертывание программного обеспечения в соответствующей ИТ-инфраструктуре.

Благодаря этому решению можно видеть, какие контейнеры работают на узлах контейнеров и какие образы выполняются в контейнерах. Также можно просматривать подробные сведения аудита, в том числе команды, используемые в контейнерах. Кроме того, вы можете устранять неполадки контейнеров, просматривая централизованные журналы и выполняя в них поиск, без необходимости удаленного просмотра узлов Docker. Решение позволяет находить контейнеры, содержащие ошибки и использующие слишком много ресурсов на узле. Также у вас есть возможность централизованно просматривать сведения об использовании и производительности ЦП, памяти, хранилища и сети по контейнерам.

## <a name="installing-and-configuring-the-solution"></a>Установка и настройка решения
Для установки и настройки решений используйте указанные ниже данные.

Решение "Контейнеры" необходимо добавить в рабочую область OMS, как описано в статье [Добавление решений Log Analytics из коллекции решений](log-analytics-add-solutions.md).

Docker с OMS можно установить и использовать двумя способами:

* В поддерживаемых операционных системах Linux: установите и запустите Docker, а затем установите и настройте агент OMS для Linux.
* В CoreOS: установите и запустите Docker, а затем настройте агент OMS для запуска в контейнере.

Ознакомиться с поддерживаемыми версиями Docker и операционными системами Linux для узла контейнера можно на портале [GitHub](https://github.com/Microsoft/OMS-docker).

> [!IMPORTANT]
> Docker необходимо запустить **перед** установкой [агента OMS для Linux](log-analytics-linux-agents.md) на узлах контейнера. Если вы уже установили агент перед установкой Docker, необходимо переустановить агент OMS для Linux. Дополнительные сведения о Docker см. на [веб-сайте Docker](https://www.docker.com).
> 
> 

Для мониторинга контейнеров необходимо настроить указанные ниже параметры на узлах контейнера.

## <a name="configure-settings-for-the-linux-container-host"></a>Настройка параметров для узла контейнера Linux
После установки Docker используйте приведенные ниже параметры узла контейнера, чтобы настроить агент для использования с Docker. CoreOS не поддерживает такой метод настройки.

### <a name="to-configure-settings-for-the-container-host---systemd-(suse,-opensuse,-centos-7.x,-rhel-7.x,-and-ubuntu-15.x-and-higher)"></a>Настройка параметров для узла контейнера — systemd (SUSE, openSUSE, CentOS 7.x, RHEL 7.x, Ubuntu 15.x и более поздние версии)
1. Добавьте в файл docker.service следующий код:
   
    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```
2. Добавьте $DOCKER\_OPTS в строку &quot;ExecStart=/usr/bin/docker daemon&quot; в файле docker.service. Используйте следующий пример:
   
    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```
3. Перезапустите службу Docker. Например:
   
    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-(ubuntu-14.x)"></a>Настройка параметров для узла контейнера — Upstart (Ubuntu 14.x)
1. Добавьте в файл /etc/default/docker следующий код:
   
    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```
2. Сохраните файл, а затем перезапустите службы Docker и OMS.
   
    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Настройка параметров для узла контейнера — Amazon Linux
1. Добавьте в файл /etc/sysconfig/docker следующий код:
   
    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```
2. Сохраните файл, а затем перезапустите службу Docker.
   
    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Настройка параметров для контейнеров CoreOS
После установки Docker используйте следующие параметры для CoreOS, чтобы запустить Docker и создать контейнер. С этим методом настройки можно использовать любую поддерживаемую версию Linux, включая CoreOS. Вам потребуется [идентификатор и ключ рабочей области OMS](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Использование OMS для всех контейнеров в CoreOS
* Запустите контейнер OMS, который вы хотите отслеживать. Используйте следующий пример, внеся в него необходимые изменения:
  
  ```
  sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
  ```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Переход от использования установленного агента к использованию агента в контейнере
Если ранее вы использовали установленный напрямую агент и теперь вместо него хотите использовать агент, работающий в контейнере, необходимо сначала удалить агент OMS. См. раздел [Steps to install the OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) (Шаги по установке агента OMS для Linux).

## <a name="containers-data-collection-details"></a>Сведения о сборе данных из контейнеров
Решение "Контейнеры" собирает различные метрики производительности и данные журналов из узла контейнеров, из контейнеров, в которых включен агент OMS для Linux, а также из агента OMS, запущенного в контейнерах.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для решения "Контейнеры".

| платформа | Агент OMS для Linux | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Да](./media/log-analytics-containers/oms-bullet-green.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |![Нет](./media/log-analytics-containers/oms-bullet-red.png) |Каждые 3 минуты |

В следующей таблице приведены примеры типов данных, собираемых решением "Контейнеры".

| Тип данных | Поля |
| --- | --- |
| Производительность узлов и контейнеров |Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Список контейнеров |TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContinerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Список образов контейнеров |TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Журнал контейнеров |TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Журнал службы контейнеров |TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |

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

Каждая область на панели мониторинга — это визуальное представление поиска по собранным данным.

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

* **ContainerInventory** — этот тип используется для получения сведений о расположении контейнеров, их именах и образах, которые в них выполняются.
* **ContainerImageInventory** — этот тип используется для поиска сведений, упорядоченных по образам, и для просмотра информации об образах, включая идентификаторы или размеры образов.
* **ContainerLog** — этот тип используется, если требуется найти определенные сведения или записи в журнале ошибок.
* **ContainerServiceLog** — этот тип используется для поиска в журнале аудита данных об управляющей программе Docker, включая команды для запуска, остановки и извлечения.

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
Сохранение запросов — это стандартная функция в Log Analytics. Она позволяет сохранять полезные запросы для использования в будущем.

Создав запрос, который вы считаете полезным, сохраните его, щелкнув **Избранное** в верхней части страницы поиска по журналам. Позднее вы сможете легко открыть его на странице **Моя панель мониторинга**.

## <a name="next-steps"></a>Дальнейшие действия
* [Выполните поиск по журналам](log-analytics-log-searches.md), чтобы просмотреть подробные записи с данными контейнеров.

<!--HONumber=Oct16_HO2-->


