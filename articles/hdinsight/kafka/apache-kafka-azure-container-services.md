---
title: Использование службы Azure Kubernetes с Kafka HDInsight | Документация Майкрософт
description: Дополнительные сведения об использовании Kafka HDInsight из образов контейнера, размещенных в службе Azure Kubernetes (AKS).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: f54039a0e702aa3c789363969120e000760f6ef5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885967"
---
# <a name="use-azure-kubernetes-service-with-kafka-on-hdinsight"></a>Использование службы Azure Kubernetes с Kafka HDInsight

В этой статье приведены сведения об использовании службы Azure Kubernetes (AKS) с Kafka в кластере HDInsight. В описанных здесь действиях подключение к Kafka проверяется с помощью приложения Node.js, размещенного в AKS. Это приложение обменивается данными с Kafka с помощью пакета [kafka-node](https://www.npmjs.com/package/kafka-node). Обмен сообщениями между клиентом браузера и внутренним приложением, размещенным в AKS, выполняется с помощью [Socket.IO](https://socket.io/).

[Apache Kafka](https://kafka.apache.org) — это распределенная платформа потоковой передачи с открытым исходным кодом, которую можно использовать для создания конвейеров и приложений потоковой передачи данных в режиме реального времени. Служба Azure Kubernetes управляет размещенной средой Kubernetes, что упрощает развертывание приложений в контейнерах. В виртуальной сети Azure можно подключить две службы.

> [!NOTE]
> В этой статье рассматриваются шаги, которые позволяют включить обмен данными между службой Azure Kubernetes и Kafka HDInsight. В качестве примера используется базовый клиент Kafka. На его основе мы рассмотрим принципы работы конфигурации.

## <a name="prerequisites"></a>предварительным требованиям

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Подписка Azure

В этой статье предполагается, что вы знаете, как создавать и использовать следующие службы Azure:

* Kafka HDInsight
* Служба Azure Kubernetes
* Виртуальные сети Azure

В этой статье предполагается, что вы ознакомились с [руководством по службе Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md). В нем создается служба контейнеров, кластер Kubernetes, реестр контейнера и настраивается служебная программа `kubectl`.

## <a name="architecture"></a>Архитектура

### <a name="network-topology"></a>Топология сети

HDInsight и AKS используют виртуальную сеть Azure в качестве контейнера вычислительных ресурсов. Чтобы обеспечить обмен данными между HDInsight и AKS, необходимо обеспечить взаимодействие между их сетями. В этой статье устанавливается пиринг между виртуальными сетями в Azure. Кроме того, должны работать и другие подключения, например VPN. Дополнительные сведения о пиринге см. в статье [Пиринг между виртуальными сетями](../../virtual-network/virtual-network-peering-overview.md).


На следующей схеме показана топология сети, используемая в этой статье:

![HDInsight находится в одной виртуальной сети, а AKS — в другой. Их сети соединены с помощью кэширующего узла.](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> Разрешение имен между одноранговыми сетями не включено, поэтому используются IP-адреса. По умолчанию при подключении клиентов Kafka в HDInsight возвращает имена узлов, а не IP-адреса. Действия, описанные в этой статье, позволяют настроить использование в Kafka объявления IP-адресов.

## <a name="create-an-azure-kubernetes-service-aks"></a>Создание службы Azure Kubernetes (AKS)

Если у вас еще нет кластера AKS, создайте его, выполнив действия, приведенные в одной из этих статей:

* [Развертывание кластера службы Azure Kubernetes (AKS) — портал](../../aks/kubernetes-walkthrough-portal.md)
* [Развертывание кластера службы Azure Kubernetes (AKS) — CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> Во время установки AKS создает виртуальную сеть. Эта сеть имеет пиринговую связь с сетью HDInsight, создание которой описано в следующем разделе.

## <a name="configure-virtual-network-peering"></a>Настройка пиринговой связи между виртуальными сетями

1. На [портале Azure](https://portal.azure.com) выберите __Группы ресурсов__, а затем найдите группу ресурсов, которая содержит виртуальную сеть кластера AKS. Имя группы ресурсов — `MC_<resourcegroup>_<akscluster>_<location>`. Записи `resourcegroup` и `akscluster` — это имя группы ресурсов, в которой создан кластер, и имя кластера. `location` — это расположение, в котором создан кластер.

2. В группе ресурсов выберите ресурс __Виртуальная сеть__.

3. Выберите __Адресное пространство__. Запишите полученное адресное пространство.

4. Чтобы создать виртуальную сеть HDInsight, выберите __+ Создать ресурс__, щелкните __Сетевые подключения__, а затем — __Виртуальная сеть__.

    > [!IMPORTANT]
    > При вводе значений новой виртуальной сети необходимо использовать адресное пространство, которое не перекрывает адресное пространство, используемое сетью кластера AKS.

    Используйте то же __расположение__ виртуальной сети, что и в кластере AKS.

    Дождитесь создания виртуальной сети, а затем перейдите к следующему шагу.

5. Чтобы настроить пиринг между сетью HDInsight и сетью кластера AKS, выберите виртуальную сеть, а затем — __Пиринги__. Выберите __+ Добавить__ и заполните форму, используя следующие значения:

    * __Имя__. Введите уникальное имя этой конфигурации пиринга.
    * __Виртуальная сеть__. Выберите в этом поле виртуальную сеть **кластера AKS**.

    Оставьте во всех остальных полях значения по умолчанию, а затем нажмите кнопку __ОК__, чтобы настроить пиринг.

6. Чтобы настроить пиринг между сетью кластера AKS и сетью HDInsight, выберите __AKS cluster virtual network__ (Виртуальная сеть кластера AKS), а затем — __Пиринги__. Выберите __+ Добавить__ и заполните форму, используя следующие значения:

    * __Имя__. Введите уникальное имя этой конфигурации пиринга.
    * __Виртуальная сеть__. Выберите в этом поле виртуальную сеть __кластера HDInsight__.

    Оставьте во всех остальных полях значения по умолчанию, а затем нажмите кнопку __ОК__, чтобы настроить пиринг.

## <a name="install-kafka-on-hdinsight"></a>Установка Kafka HDInsight

При создании Kafka в кластере HDInsight необходимо подключиться к созданной ранее виртуальной сети HDInsight. Дополнительные сведения о создании кластера Kafka см. в статье [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

> [!IMPORTANT]
> При создании кластера необходимо присоединиться к созданной виртуальной сети HDInsight, используя __дополнительные параметры__.

## <a name="configure-kafka-ip-advertising"></a>Настройка объявления IP-адресов в Kafka

Чтобы настроить объявление IP-адресов в Kafka вместо доменных имен, сделайте следующее:

1. Откройте веб-браузер и перейдите по адресу https://CLUSTERNAME.azurehdinsight.net. Замените __CLUSTERNAME__ именем кластера Kafka HDInsight.

    При появлении запроса введите имя пользователя и пароль HTTPS для кластера. Отобразится веб-интерфейс Ambari для кластера.

2. Чтобы просмотреть сведения о Kafka, из списка слева выберите __Kafka__.

    ![Список служб с выделенной службой Kafka](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Чтобы просмотреть конфигурацию Kafka, выберите пункт __Configs__ (Конфигурации) в верхней части окна.

    ![Ссылка на пункт "Configs" (Конфигурации) для Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Чтобы найти конфигурацию __kafka-env__, введите `kafka-env` в поле __фильтра__ в правом верхнем углу.

    ![Конфигурация Kafka, kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

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

    ![Кнопка сохранения конфигурации](./media/apache-kafka-azure-container-services/save-button.png)

9. Для предотвращения ошибок при перезапуске Kafka нажмите кнопку __Service Actions__ (Действия со службой) и выберите __Turn On Maintenance Mode__ (Включить режим обслуживания). Чтобы завершить эту операцию, нажмите кнопку "ОК".

    ![Кнопка "Service Actions" (Действия со службой) с выделенной командой "Turn On Maintenance Mode" (Включить режим обслуживания)](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Чтобы перезапустить Kafka, нажмите кнопку __Restart__ (Перезапустить) и выберите __Restart All Affected__ (Перезапустить все затронутые). Подтвердите перезапуск, а после завершения операции нажмите кнопку __ОК__.

    ![Кнопка "Restart" (Перезапустить) с выделенной командой "Restart All Affected" (Перезапустить все затронутые)](./media/apache-kafka-azure-container-services/restart-button.png)

11. Чтобы отключить режим обслуживания нажмите кнопку __Service Actions__ (Действия со службой) и выберите __Turn Off Maintenance Mode__ (Отключить режим обслуживания). Чтобы завершить эту операцию, нажмите кнопку **ОК**.

## <a name="test-the-configuration"></a>Тестирование конфигурации

На этом этапе Kafka и службы Azure Kubernetes взаимодействуют через пиринговые виртуальные сети. Чтобы проверить это подключение, выполните следующие действия:

1. Создайте раздел Kafka, используемый примером приложения. Дополнительные сведения о создании разделов Kafka см. в статье [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

2. Скачайте пример приложения из [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test).

3. Измените файл `index.js`, добавив следующие строки:

    * `var topic = 'mytopic'`. Замените `mytopic` именем раздела Kafka, используемого этим приложением.
    * `var brokerHost = '176.16.0.13:9092`. Замените `176.16.0.13` внутренним IP-адресом одного из узлов брокера кластера.

        Сведения о том, как получить внутренний IP-адрес узлов брокера (рабочих узлов) в кластере, см. в этом [разделе](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes). Выберите IP-адрес одной из записей, чье доменное имя начинается с `wn`.

4. Из командной строки в каталоге `src` установите зависимости и создайте образ развертывания с помощью Docker:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Пакеты, необходимые этому приложению, записываются в репозиторий после изменения, поэтому, чтобы установить их, больше не требуется служебная программа `npm`.

5. Войдите в Реестр контейнеров Azure и найдите имя loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Если вы не знаете имя Реестра контейнеров Azure или не работали с AKS через Azure CLI, ознакомьтесь с [этим руководством](../../aks/tutorial-kubernetes-prepare-app.md).

6. Пометьте локальный образ `kafka-aks-test` тегом loginServer ACR. Кроме того, добавьте в конце `:v1`, чтобы указать версию образа:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Отправьте образ в реестр:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Эта операция занимает несколько минут.

8. Измените файл манифеста Kubernetes (`kafka-aks-test.yaml`) и замените `microsoft` именем loginServer ACR, полученным на шаге 4.

9. Чтобы развернуть параметры приложения из манифеста, выполните следующую команду:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Чтобы наблюдать за внешним IP-адресом (`EXTERNAL-IP`) приложения, выполните следующую команду:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    После присвоения внешнего IP-адреса нажмите клавиши __CTRL+C__, чтобы выйти из режима наблюдения.

11. Откройте браузер и введите внешний IP-адрес службы. Вы должны перейти на страницу, аналогичную показанной ниже:

    ![Изображение веб-страницы](./media/apache-kafka-azure-container-services/test-web-page.png)

12. В поле введите текст, а затем нажмите кнопку __Отправить__. Данные отправляются в Kafka. Затем объект-получатель Kafka в приложении читает сообщение и добавляет его в раздел __Messages from Kafka__ (Сообщения из Kafka).

    > [!WARNING]
    > Вы можете получить несколько копий сообщения. Эта проблема обычно возникает при обновлении страницы в браузере после подключения или при открытии нескольких подключений браузера к приложению.

## <a name="next-steps"></a>Дополнительная информация

Ниже приведены ссылки на статьи об использовании Apache Kafka в HDInsight.

* [Get started with Apache Kafka on HDInsight (preview)](apache-kafka-get-started.md) (Приступая к работе с Apache Kafka в HDInsight (предварительная версия))

* [Use MirrorMaker to create a replica of a Kafka on HDInsight cluster (preview)](apache-kafka-mirroring.md) (Создание реплики Kafka в кластере HDInsight с помощью MirrorMaker (предварительная версия))

* [Совместное использование Apache Kafka (предварительная версия) и Storm в HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Совместное использование Apache Spark и Kafka (предварительная версия) в HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Подключение к Kafka через виртуальную сеть Azure](apache-kafka-connect-vpn-gateway.md)
