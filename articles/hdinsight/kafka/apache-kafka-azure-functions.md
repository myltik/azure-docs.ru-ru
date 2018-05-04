---
title: Отправка данных в Kafka HDInsight с помощью службы "Функции Azure" | Документация Майкрософт
description: Сведения о том, как с помощью службы "Функции Azure" записывать данные в Kafka HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: 6c462f9fe5e152c82be1a2b8643ee35d260a90f6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Использование Kafka HDInsight из приложения-функции Azure

В этой статье описано, как создать приложение-функцию Azure, которое отправляет данные в Kafka HDInsight.

[Функции Azure](https://docs.microsoft.com/azure/azure-functions/) — это независимая от сервера служба вычислений. Она позволяет выполнять код по требованию без необходимости явно подготавливать или администрировать инфраструктуру.

[Apache Kafka](https://kafka.apache.org) — это распределенная платформа потоковой передачи с открытым исходным кодом, которую можно использовать для создания конвейеров и приложений потоковой передачи данных в режиме реального времени. Kafka также предоставляет функцию брокера сообщений, подобную очереди сообщений, с помощью которой можно выполнять публикацию и подписываться на именованные потоки данных. Kafka в HDInsight предоставляет управляемую, высокомасштабируемую службу с высоким уровнем доступности в облаке Microsoft Azure.

Kafka HDInsight не предоставляет API, обеспечивающий общий доступ через Интернет. Чтобы опубликовать данные в Kafka или получить данные из этой платформы, необходимо подключиться к кластеру Kafka через виртуальную сеть Azure. Служба "Функции Azure" предоставляет удобный способ создания общедоступной конечной точки, которая отправляет данные в Kafka HDInsight через шлюз виртуальной сети.

> [!NOTE]
> В этом документе рассматриваются шаги, которые позволяют включить обмен данными между службой "Функции Azure" и Kafka HDInsight. В качестве примера используется базовый производитель Kafka. На его основе мы рассмотрим принципы работы конфигурации.

## <a name="prerequisites"></a>предварительным требованиям

* Приложение-функция Azure. Дополнительные сведения см. в разделе [Создание функции, активируемой HTTP](../../azure-functions/functions-create-first-azure-function.md).

* Виртуальная сеть Azure. Чтобы работать со службой "Функции Azure", в виртуальной сети необходимо использовать один из следующих диапазонов IP-адресов:

    * 10.0.0.0–10.255.255.255
    * 172.16.0.0–172.31.255.255
    * 192.168.0.0–192.168.255.255

    Дополнительные сведения см. в статье [Интеграция приложения с виртуальной сетью Azure](../../app-service/web-sites-integrate-with-vnet.md).

* Kafka в кластере HDInsight. Дополнительные сведения по созданию Kafka в кластере HDInsight см. в статье [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

    > [!IMPORTANT]
    > Настройка кластера выполняется с использованием __дополнительных параметров__, в которых можно выбрать виртуальную сеть и подсеть Azure, которую использует HDInsight. Выберите виртуальную сеть и подсеть, созданные на предыдущем шаге.

    Дополнительные сведения о Kafka и виртуальных сетях см. в статье [Подключение к Kafka в HDInsight с помощью виртуальной сети Azure](apache-kafka-connect-vpn-gateway.md).

## <a name="architecture"></a>Архитектура

Kafka HDInsight содержится в виртуальной сети Azure. Служба "Функции Azure" может взаимодействовать с виртуальной сетью через шлюз типа "точка — сеть". Ниже представлена схема этой топологии сети.

![Архитектура подключения службы "Функции Azure" к HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Настройка Kafka

В этом разделе содержится информация о подготовке кластера Kafka к приему данных из службы "Функции Azure". Здесь рассматриваются такие действия настройки:

* объявления IP-адресов;
* сбор IP-адресов брокеров Kafka;
* создание раздела Kafka.

### <a name="configure-kafka-for-ip-advertising"></a>Настройка Kafka для объявления IP-адресов

По умолчанию Zookeeper возвращает клиентам доменное имя брокеров Kafka. Эта конфигурация не работает в отсутствии DNS-сервера, так как клиенту (службе "Функции Azure") не удается разрешить имена виртуальной сети. Для этой конфигурации выполните следующие действия, чтобы настроить Kafka для объявления IP-адресов вместо доменных имен:

1. Откройте веб-браузер и перейдите по адресу https://CLUSTERNAME.azurehdinsight.net. Замените __CLUSTERNAME__ именем кластера Kafka HDInsight.

    При появлении запроса введите имя пользователя и пароль HTTPS для кластера. Отобразится веб-интерфейс Ambari для кластера.

2. Чтобы просмотреть сведения о Kafka, из списка слева выберите __Kafka__.

    ![Список служб с выделенной службой Kafka](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Чтобы просмотреть конфигурацию Kafka, выберите пункт __Configs__ (Конфигурации) в верхней части окна.

    ![Ссылка на пункт "Configs" (Конфигурации) для Kafka](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Чтобы найти конфигурацию __kafka-env__, введите `kafka-env` в поле __фильтра__ в правом верхнем углу.

    ![Конфигурация Kafka, kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

5. Чтобы настроить Kafka для объявления IP-адресов, добавьте следующий текст в нижнюю часть поля __kafka-env template__ (шаблон kafka-env):

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Чтобы настроить интерфейс, через который Kafka ожидает передачи данных, введите `listeners` в поле __фильтра__ в правом верхнем углу.

7. Чтобы настроить Kafka для ожидания передачи данных через все сетевые интерфейсы, измените значение в поле __listeners__ на `PLAINTEXT://0.0.0.0:9092`.

8. Нажмите кнопку __Save__ (Сохранить), чтобы сохранить изменения в конфигурации. Введите текст, описывающий изменения. После сохранения изменений нажмите кнопку __ОК__.

    ![Кнопка сохранения конфигурации](./media/apache-kafka-azure-functions/save-button.png)

9. Для предотвращения ошибок при перезапуске Kafka нажмите кнопку __Service Actions__ (Действия со службой) и выберите __Turn On Maintenance Mode__ (Включить режим обслуживания). Чтобы завершить эту операцию, нажмите кнопку "ОК".

    ![Кнопка "Service Actions" (Действия со службой) с выделенной командой "Turn On Maintenance Mode" (Включить режим обслуживания)](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Чтобы перезапустить Kafka, нажмите кнопку __Restart__ (Перезапустить) и выберите __Restart All Affected__ (Перезапустить все затронутые). Подтвердите перезапуск, а после завершения операции нажмите кнопку __ОК__.

    ![Кнопка "Restart" (Перезапустить) с выделенной командой "Restart All Affected" (Перезапустить все затронутые)](./media/apache-kafka-azure-functions/restart-button.png)

11. Чтобы отключить режим обслуживания нажмите кнопку __Service Actions__ (Действия со службой) и выберите __Turn Off Maintenance Mode__ (Отключить режим обслуживания). Чтобы завершить эту операцию, нажмите кнопку **ОК**.

### <a name="get-the-kafka-broker-ip-address"></a>Получение IP-адресов брокеров Kafka

Чтобы получить полное доменное имя (FQDN) и IP-адреса узлов кластера Kafka, используйте один из следующих методов.

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

В этой команде предполагается, что `<resourcegroupname>` — это имя группы ресурсов Azure, содержащей виртуальную сеть.

В списке возвращаемых имен выберите IP-адрес рабочего узла. Внутреннее полное доменное имя узла начинается с букв `wn`. Через этот IP-адрес функция взаимодействует с Kafka.

### <a name="create-a-kafka-topic"></a>Создание раздела Kafka

Kafka сохраняет данные в __разделах__. Перед отправкой данных в Kafka из службы "Функции Azure" необходимо создать функцию.

Чтобы создать раздел, выполните действия, описанные в статье [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

## <a name="create-a-function-that-sends-to-kafka"></a>Создание функции, отправляющей данные в Kafka

> [!NOTE]
> В этом разделе показано, как создать функцию JavaScript, которая публикует данные в Kafka с помощью пакета [kafka-node](https://www.npmjs.com/package/kafka-node):

1. Создайте функцию __Веб-перехватчик + API__ и выберите язык __JavaScript__. Дополнительные сведения о создании функций см. в статье [Создание первой функции на портале Azure](../../azure-functions/functions-create-first-azure-function.md#create-function).

2. Используйте следующий код в качестве текста функции:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Замените `'test'` именем раздела Kafka, созданного в кластере HDInsight.

    Замените `10.1.0.7` IP-адресом, полученным ранее. Оставьте значение `:9092`. 9092 — это порт, который прослушивает Kafka.

    Используйте кнопку __Сохранить__ , чтобы сохранить изменения.

    ![Кнопка "Сохранить" в редакторе](./media/apache-kafka-azure-functions/function-editor.png)

3. В правой части редактора функции выберите __Просмотреть файлы__. Выберите __+ Добавить__ и добавьте новый файл с именем `package.json`. С помощью этого файла можно указать зависимость в пакете `kafka-node`.

    ![Добавление файла](./media/apache-kafka-azure-functions/add-files.png)

4. Чтобы изменить новый файл, выберите `package.json` из списка __файлов__. В качестве содержимого файла добавьте следующий текст:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Используйте кнопку __Сохранить__ , чтобы сохранить изменения.

5. Чтобы установить пакет `kafka-node`, ознакомьтесь с разделом _Управление версиями и пакетами Node_ из [руководства разработчика JavaScript для Функций Azure](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > При установке пакета kafka-node может возникнуть несколько ошибок. Эти ошибки можно игнорировать.

## <a name="run-the-function"></a>Выполнение функции

В правой части редактора функции выберите __Проверить__. Оставьте настройки по умолчанию, а затем выберите __Выполнить__. Во время тестового запуска функция получает параметр `name`. Этот параметр содержит значение `Azure`, которое функция вставляет в Kafka.

![Снимок экрана диалогового окна теста](./media/apache-kafka-azure-functions/function-test-dialog.png)

Чтобы просмотреть информацию, регистрируемую функцией при тестовых запусках, выберите __Журналы__ в нижней части страницы. Выполните тест еще раз, чтобы записать данные в журнал.

![Пример выходных данных журнала функции](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Проверка данных в Kafka

Чтобы проверить, переданы ли данные в раздел Kafka, изучите раздел _Создание и использование записей_ статьи [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md#produce-and-consume-records). `kafka-console-consumer` считывает данные из раздела и отображает список хранящихся в нем сообщений.

## <a name="next-steps"></a>Дополнительная информация

Ниже приведены ссылки на статьи об использовании Apache Kafka в HDInsight.

* [Get started with Apache Kafka on HDInsight (preview)](apache-kafka-get-started.md) (Приступая к работе с Apache Kafka в HDInsight (предварительная версия))

* [Use MirrorMaker to create a replica of a Kafka on HDInsight cluster (preview)](apache-kafka-mirroring.md) (Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия))

* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Подключение к Kafka через виртуальную сеть Azure](apache-kafka-connect-vpn-gateway.md)
