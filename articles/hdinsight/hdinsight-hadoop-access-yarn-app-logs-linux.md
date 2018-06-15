---
title: Доступ к журналам приложений Hadoop YARN в HDInsight под управлением Linux — Azure | Документы Майкрософт
description: Узнайте, как получить доступ к журналам приложений YARN в кластере HDInsight (Hadoop) под управлением Linux с помощью как командной строки, так и веб-браузера.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: larryfr
ms.openlocfilehash: 67732b3a4c63408d8fc6ab5ed75b40a0eb043167
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31392244"
---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Доступ к журналам приложений YARN в HDInsight под управлением Linux

Узнайте, как получить доступ к журналам приложений YARN (Yet Another Resource Negotiator) в кластере Hadoop в Azure HDInsight.

> [!IMPORTANT]
> Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Что представляют собой различные компоненты и версии Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md#hdinsight-windows-retirement)

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[YARN Timeline Server](http://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) предоставляет общие сведения о завершенных приложениях, а также зависящие от платформы сведения о приложениях через два разных интерфейса. В частности:

* Возможность хранения и извлечения общей информации о приложениях в кластерах HDInsight появилась, начиная с версии 3.1.1.374.
* В настоящее время компонент сервера временной шкалы, предоставляющий сведения о приложениях для конкретной платформы, недоступен в кластерах HDInsight.

К общим сведениям о приложениях относятся следующие типы данных:

* уникальный идентификатор приложения;
* имя пользователя, запустившего приложение;
* информация о попытках завершить приложение;
* информация о контейнерах, которые использовались во время каждой из попыток.

## <a name="YARNAppsAndLogs"></a>Приложения и журналы YARN

YARN поддерживает несколько моделей программирования (в том числе MapReduce), отделяя управление ресурсами от планирования и мониторинга приложений. YARN использует глобальный диспетчер *ResourceManager*, *диспетчеры узлов* на каждый рабочий узел и *диспетчеры приложений* на каждое приложение. Диспетчер приложений согласовывает ресурсы (ЦП, память, диск, сеть), необходимые для работы приложения, с диспетчером ресурсов. Диспетчер ресурсов совместно с диспетчером узлов предоставляют эти ресурсы в виде *контейнеров*. Диспетчер приложений отвечает за отслеживание хода выполнения контейнеров, назначаемых ему диспетчером ресурсов. Приложению может потребоваться много контейнеров в зависимости от характера приложения.

Для выполнения приложения может потребоваться несколько *попыток*. Если выполнение приложения завершается ошибкой, его можно повторить как новую попытку. Каждая попытка выполняется в контейнере. В некотором смысле контейнер обеспечивает контекст основной единице работы, выполняемой приложением YARN, и вся работа, выполняемая в контексте контейнера, выполняется на одном рабочем узле, где был выделен контейнер. Дополнительные сведения см. в статье об [основных понятиях YARN][YARN-concepts].

Журналы приложений (и соответствующие журналы контейнеров) крайне важны для отладки проблемных приложений Hadoop. YARN предоставляет хорошую платформу для сбора, объединения и хранения журналов приложений с функцией [объединения журналов][log-aggregation]. Функция объединения журналов делает доступ к журналам приложений более детерминированным, так как она объединяет журналы со всех контейнеров на рабочем узле и хранит их как один сводный файл журнала на рабочем узле в файловой системе по умолчанию после завершения приложения. Ваше приложение может использовать сотни или тысячи контейнеров, но журналы для всех контейнеров, выполненных на одном рабочем узле, всегда объединяются в один файл. Поэтому на каждый рабочий узел, используемый приложением, приходится один файл журнала. Объединение журналов включено по умолчанию на кластерах HDInsight версии 3.0 и более поздних версий. Объединенные журналы находятся в хранилище по умолчанию для кластера. В качестве пути HDFS к журналам используется следующий путь:

    /app-logs/<user>/logs/<applicationId>

В пути `user` — это имя пользователя, запустившего приложение. `applicationId` является уникальным идентификатором, назначенным YARN RM приложению.

Сводные журналы не могут считываться напрямую, так как они записаны в [TFile][T-file], [двоичном формате][binary-format], индексированном контейнером. Чтобы отобразить эти журналы для интересующих вас приложений или контейнеров в виде обычного текста, используйте журналы YARN ResourceManager или средства CLI.

## <a name="yarn-cli-tools"></a>Средства CLI для YARN

Чтобы использовать инструменты интерфейса командной строки для YARN, необходимо сначала подключиться к кластеру HDInsight с помощью SSH. См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Эти журналы можно отобразить в виде обычного текста, запустив одну из указанных ниже команд.

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

При выполнении этих команд необходимо указать параметры &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> и &lt;worker-node-address>.

## <a name="yarn-resourcemanager-ui"></a>Пользовательский интерфейс YARN ResourceManager

Пользовательский интерфейс YARN ResourceManager работает на головном узле кластера. Доступ к нему осуществляется через веб-интерфейс Ambari. Чтобы просмотреть журналы YARN, выполните следующие действия:

1. В браузере перейдите по адресу https://CLUSTERNAME.azurehdinsight.net. Параметр CLUSTERNAME нужно заменить именем кластера HDInsight.
2. В списке служб в левой части страницы выберите **YARN**.

    ![Выбранные службы Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. В раскрывающемся списке **Быстрые ссылки** выберите один из главных узлов кластера, а затем щелкните **Журнал Resource Manager**.

    ![Быстрые ссылки для Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Отобразится список ссылок на журналы YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
