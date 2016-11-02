<properties
    pageTitle="Доступ к журналам приложений Hadoop YARN в HDInsight под управлением Linux | Microsoft Azure"
    description="Узнайте, как получить доступ к журналам приложений YARN в кластере HDInsight (Hadoop) под управлением Linux с помощью как командной строки, так и веб-браузера."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="Blackmist" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="larryfr"/>


# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Доступ к журналам приложений YARN в HDInsight под управлением Linux 

В данном документе рассказывается, как получить доступ к журналам приложений YARN, завершивших работу в кластере Hadoop в Azure HDInsight.

> [AZURE.NOTE] Информация, приведенная в этом документе, относится только к кластерам HDInsight на платформе Linux. Сведения о кластерах под управлением Windows см. в статье [Программный доступ к журналам приложений YARN в Hadoop в HDInsight](hdinsight-hadoop-access-yarn-app-logs.md).

## <a name="prerequisites"></a>Предварительные требования

* Кластер HDInsight под управлением Linux.

* Чтобы получить доступ к пользовательскому веб-интерфейсу журналов ResourceManager, необходимо [создать туннель SSH](hdinsight-linux-ambari-ssh-tunnel.md) .

## <a name="<a-name="yarntimelineserver"></a>yarn-timeline-server"></a><a name="YARNTimelineServer"></a>YARN Timeline Server

[YARN Timeline Server](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) предоставляет общие сведения о завершенных приложениях, а также зависящие от платформы сведения о приложениях через два разных интерфейса. В частности:

* Возможность хранения и извлечения общей информации о приложениях в кластерах HDInsight появилась, начиная с версии 3.1.1.374.
* В настоящее время компонент сервера временной шкалы, предоставляющий сведения о приложениях для конкретной платформы, недоступен в кластерах HDInsight.

К общим сведениям о приложениях относятся следующие типы данных:

* уникальный идентификатор приложения;
* имя пользователя, запустившего приложение;
* информация о попытках завершить приложение;
* информация о контейнерах, которые использовались во время каждой из попыток.

## <a name="<a-name="yarnappsandlogs"></a>yarn-applications-and-logs"></a><a name="YARNAppsAndLogs"></a>Приложения и журналы YARN

YARN поддерживает несколько моделей программирования (в том числе MapReduce), отделяя управление ресурсами от планирования и мониторинга приложений. Это осуществляется с помощью глобального диспетчера *ResourceManager*, *диспетчеров узлов*, на каждый рабочий узел и *диспетчеров приложений* на каждое приложение. Диспетчер приложений согласовывает ресурсы (ЦП, память, диск, сеть), необходимые для работы приложения, с диспетчером ресурсов. Диспетчер ресурсов совместно с диспетчером узлов предоставляют эти ресурсы в виде *контейнеров*. Диспетчер приложений отвечает за отслеживание хода выполнения контейнеров, назначаемых ему диспетчером ресурсов. Приложению может потребоваться много контейнеров в зависимости от характера приложения.

Кроме того, для выполнения приложения при наличии сбоев или из-за потери связи между диспетчером приложений и Resource Manager может потребоваться несколько *попыток*. Таким образом, контейнеры предоставляются определенной попытке. В некотором смысле контейнер обеспечивает контекст основной единице работы, выполняемой приложением YARN, и вся работа, выполняемая в контексте контейнера, выполняется на одном рабочем узле, где был выделен контейнер. Дополнительную информацию см. в статье об [основных понятиях YARN][YARN-concepts].

Журналы приложений (и соответствующие журналы контейнеров) крайне важны для отладки проблемных приложений Hadoop. YARN предоставляет хорошую платформу для сбора, объединения и хранения журналов приложений с функцией [объединения журналов][log-aggregation]. Функция объединения журналов делает доступ к журналам приложений более детерминированным, так как она объединяет журналы со всех контейнеров на рабочем узле и хранит их как один сводный файл журнала в файловой системе по умолчанию после завершения приложения. Ваше приложение может использовать сотни или тысячи контейнеров, но журналы для всех контейнеров, выполненных на одном рабочем узле, всегда будут объединены в один файл. В результате на каждый рабочий узел, используемый приложением, приходится один файл журнала. Объединение журналов включено по умолчанию в кластерах HDInsight (3.0 и более поздних версий), а сводные журналы можно найти в контейнере кластера по умолчанию по следующему адресу:

    wasbs:///app-logs/<user>/logs/<applicationId>

где *user* — имя пользователя, запустившего приложение, а *applicationId*— уникальный идентификатор приложения, назначенный диспетчером ресурсов YARN.

Объединенные журналы невозможно считывать напрямую, так как они записаны в [TFile][T-file] — [двоичном формате][binary-format], индексированном контейнером. Чтобы отобразить эти журналы для интересующих вас приложений или контейнеров в виде обычного текста, необходимо использовать журналы YARN ResourceManager или средства CLI. 

##<a name="yarn-cli-tools"></a>Средства CLI для YARN

Чтобы использовать средства CLI для YARN, необходимо сначала подключиться к кластеру HDInsight с помощью SSH. Сведения об использовании SSH совместно с HDInsight см. в указанных ниже документах.

- [Использование SSH с Hadoop под управлением Linux в HDInsight в Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

- [Использование SSH с Hadoop под управлением Linux в HDInsight в Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Эти журналы можно отобразить в виде обычного текста, запустив одну из указанных ниже команд.

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    
При запуске этих команд необходимо указать параметры &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> и &ltworker-node-address>.

##<a name="yarn-resourcemanager-ui"></a>Пользовательский интерфейс YARN ResourceManager

Пользовательский интерфейс диспетчера ресурсов YARN работает на главном узле кластера. Доступ к нему можно получить с помощью пользовательского веб-интерфейса Ambari. Тем не менее для получения доступа к пользовательскому интерфейсу диспетчера ресурсов необходимо [создать туннель SSH](hdinsight-linux-ambari-ssh-tunnel.md).

После создания туннеля SSH для просмотра журналов YARN выполните указанные ниже действия.

1. В веб-браузере перейдите на сайт https://имя_кластера.azurehdinsight.net. Параметр CLUSTERNAME нужно заменить именем кластера HDInsight.

2. В списке служб в левой части страницы выберите __YARN__.

    ![Выбранные службы Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. В раскрывающемся списке __Быстрые ссылки__ выберите один из главных узлов кластера, а затем щелкните __Журнал Resource Manager__.

    ![Быстрые ссылки для Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
    
    Отобразится список ссылок на журналы YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/



<!--HONumber=Oct16_HO2-->


