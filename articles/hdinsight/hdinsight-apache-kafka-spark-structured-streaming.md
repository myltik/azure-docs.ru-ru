---
title: "Структурированная потоковая передача Apache Spark с Kafka в Azure HDInsight | Документы Майкрософт"
description: "Узнайте об использовании потоковой передачи Apache Spark (DStream) для двунаправленного обмена данными с Apache Kafka. В этом примере показано, как выполнить потоковую передачу данных, используя записную книжку Jupyter из Spark в HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: 9eb39989bdec330e47e6233be5c1347ff716bed2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="use-spark-structured-streaming-with-kafka-preview-on-hdinsight"></a>Использование структурированной потоковой передачи Spark с Kafka (предварительная версия) в HDInsight

Узнайте, как использовать структурированную потоковую передачу Spark для чтения данных из Apache Kafka в Azure HDInsight.

Структурированная потоковая передача Spark — это механизм обработки потока, встроенный в Spark SQL. Он позволяет выражать потоковые вычисления так же, как пакетные вычисления статических данных. Дополнительные сведения о структурированной потоковой передаче см. в разделе [Руководство по программированию структурированной потоковой передачи [альфа-версия]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) на Apache.org.

> [!IMPORTANT]
> В этом примере используется Spark 2.1 в HDInsight 3.6. Структурированная потоковая передача рассматривается как __альфа-версия__ в Spark 2.1.
>
> Вы узнаете, как создать группу ресурсов Azure, которая содержит кластеры Spark и Kafka в HDInsight. Оба этих кластера находятся в виртуальной сети Azure, что позволяет кластеру Spark напрямую обмениваться данными с кластером Kafka.
>
> Выполнив инструкции, не забудьте удалить кластеры, чтобы избежать ненужных расходов.

## <a name="create-the-clusters"></a>Создание кластеров

Apache Kafka в HDInsight не предоставляет доступ к брокерам Kafka через общедоступный сегмент Интернета. Все объекты, обращающиеся к Kafka, должны находиться в той же виртуальной сети Azure, что и узлы в кластере Kafka. В этом примере кластеры Kafka и Spark расположены в виртуальной сети Azure. На следующей схеме показано, как взаимодействуют кластеры.

![Схема кластеров Spark и Kafka в виртуальной сети Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Служба Kafka ограничена обменом данными в пределах виртуальной сети. Другие службы в кластере, например SSH и Ambari, могут быть доступны через Интернет. Дополнительные сведения об общих портах, доступных в HDInsight, см. в статье [Порты и универсальные коды ресурсов (URI), используемые кластерами HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Хотя виртуальную сеть Azure, а также кластеры Kafka и Spark можно создать вручную, проще использовать шаблон Azure Resource Manager. Выполните следующие действия, чтобы развернуть виртуальную сеть Azure, а также кластеры Kafka и Spark в подписке Azure.

1. Нажмите эту кнопку, чтобы войти в Azure и открыть шаблон на портале Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Шаблон Azure Resource Manager находится здесь: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    Этот шаблон создает следующие ресурсы:

    * Kafka в кластере HDInsight 3.5;
    * Spark в кластере HDInsight 3.6;
    * виртуальную сеть Azure, содержащую кластеры HDInsight.

    > [!IMPORTANT]
    > Записная книжка структурированной потоковой передачи, используемая в этом примере, требует Spark в HDInsight 3.6. Если используется более ранняя версия Spark в HDInsight, возникнут ошибки при использовании этой записной книжки.

2. Используйте следующие сведения, чтобы заполнить раздел **Настраиваемое развертывание**:
   
    ![Настраиваемое развертывание в HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Группа ресурсов.** Создайте новую группу ресурсов или выберите существующую. Эта группа содержит кластер HDInsight.

    * **Расположение.** Выберите близкое к вам географическое расположение.

    * **Базовое имя кластера**. Это значение будет использоваться в качестве базового имени для кластеров Spark и Kafka. Например, если ввести **hdi**, будет создан кластер Spark с именем spark-hdi__ и кластер Kafka с именем **kafka-hdi**.

    * **Cluster Login User Name** (Имя пользователя для входа в кластер). Имя администратора для кластеров Spark и Kafka.

    * **Cluster Login User Password** (Пароль пользователя для входа в кластер). Имя администратора для кластеров Spark и Kafka.

    * **Имя пользователя SSH.** Создаваемый пользователь SSH для кластеров Spark и Kafka.

    * **Пароль SSH.** Пароль пользователя SSH для кластеров Spark и Kafka.

3. Прочтите **условия использования** и установите флажок **Я принимаю указанные выше условия**.

4. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Приобрести**. Процесс создания кластеров занимает около 20 минут.

Когда ресурсы будут созданы, отобразится страница со сводными сведениями.

![Сведения о группе ресурсов для виртуальной сети и кластеров](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Обратите внимание, что кластерам HDInsight присвоены имена **spark-BASENAME** и **kafka-BASENAME**, где BASENAME — имя, указанное в шаблоне. Эти имена будут использоваться позже при подключении к кластерам.

## <a name="get-the-kafka-brokers"></a>Получение брокеров Kafka

Код в этом примере подключается к узлам брокера Kafka в кластере Kafka. Чтобы найти адреса двух узлов брокера Kafka, используйте следующий пример PowerShell или Bash:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

При появлении запроса введите пароль для учетной записи администратора, чтобы войти в кластер.

> [!NOTE]
> В этом примере в `$CLUSTERNAME` должно содержаться имя кластера Kafka.
>
> В этом примере используется служебная программа [jq](https://stedolan.github.io/jq/) для анализа данных из документа JSON.

Результат будет аналогичен приведенному ниже:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Сохраните эти сведения, так как они вам понадобятся в следующих разделах этого документа.

## <a name="get-the-notebooks"></a>Получение записных книжек

Код для примера, описанного в этом документе, доступен по адресу [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Отправка записных книжек

Чтобы отправить записные книжки из проекта в кластер Spark в HDInsight, выполните следующие действия.

1. В веб-браузере подключитесь к записной книжке Jupyter в вашем кластере Spark. В следующем URL-адресе замените `CLUSTERNAME` на имя вашего кластера Kafka:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    При появлении запроса введите имя администратора кластера и пароль, которые использовались при создании кластера.

2. В правой верхней части страницы нажмите кнопку __Отправить__, чтобы отправить файл __Stream-Tweets-To_Kafka.ipynb__ в кластер. Нажмите __Открыть__ для запуска отправки.

    ![Использование кнопки "Отправить" для выбора и загрузки блокнота](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Выбор файла KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Найдите запись __Stream-Tweets-To_Kafka.ipynb__ в списке записных книжек, а затем нажмите расположенную рядом кнопку __Отправка__.

    ![Чтобы отправить записную книжку, нажмите кнопку отправки рядом с записью KafkaStreaming.ipynb.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)

4. Повторите шаги 1–3 для загрузки записной книжки __Spark-Structured-Streaming-From-Kafka.ipynb__.

## <a name="load-tweets-into-kafka"></a>Загрузка твитов в Kafka

После отправки файлов выберите запись __Stream-Tweets-To_Kafka.ipynb__, чтобы открыть записную книжку. Выполните действия в записной книжке, чтобы загрузить твиты в Kafka.

## <a name="process-tweets-using-spark-structured-streaming"></a>Обработка твитов с помощью структурированной потоковой передачи

На домашней странице записной книжки Jupyter выберите запись __Spark-Structured-Streaming-From-Kafka.ipynb__. Выполните действия в записной книжке, чтобы загрузить твиты из Kafka с помощью структурированной потоковой передачи Spark.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать структурированную потоковую передачу Spark, перейдите к следующим документам для углубленного изучения работы со Spark и Kafka.

* [Как использовать потоковую передачу Spark (DStream) с Kafka](hdinsight-apache-spark-with-kafka.md).
* [Начало работы с записной книжкой Jupyter и Spark в HDInsight](spark/apache-spark-jupyter-spark-sql.md).