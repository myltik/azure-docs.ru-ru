<properties
	pageTitle="Использование пустых граничных узлов в HDInsight | Microsoft Azure"
	description="Сведения о том, как добавить пустой граничный узел в кластер HDInsight, который может использоваться в качестве клиента, а также тестировать и разместить приложения HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="jgao"/>

# Использование пустых граничных узлов в HDInsight

Cведения о том, как добавить пустой граничный узел в кластер HDInsight под управлением Linux. Пустой граничный узел — это виртуальная машина Linux, где установлены и настроены те же клиентские средства, что и на головных узлах, но без служб Hadoop. Граничный узел можно использовать для доступа к кластеру, а также тестирования и размещения клиентских приложений.

Пустой граничный узел можно добавить в имеющийся кластер HDInsight или в новый кластер при его создании. Добавление пустого граничного узла осуществляется с помощью шаблона Azure Resource Manager. В следующем примере показано, как это сделать:

    "resources": [
		{
			"name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
			"type": "Microsoft.HDInsight/clusters/applications",
			"apiVersion": "[variables('clusterApiVersion')]",
			"properties": {
				"marketPlaceIdentifier": "EmptyNode",
				"computeProfile": {
					"roles": [{
						"name": "edgenode",
						"targetInstanceCount": 1,
						"hardwareProfile": {
							"vmSize": "[parameters('edgeNodeSize')]"
						}
					}]
				},
				"installScriptActions": [{
					"name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
					"uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
					"roles": ["edgenode"]
				}],
				"uninstallScriptActions": [],
				"httpsEndpoints": [],
				"applicationType": "CustomApplication"
			}
		}
	],

Как показано в примере, при необходимости можно вызвать [действие скрипта](hdinsight-hadoop-customize-cluster-linux.md), чтобы выполнить дополнительные настройки, такие как установка [Apache Hue](hdinsight-hadoop-hue-linux.md) на граничный узел.

После создания граничного узла можно подключиться к нему с помощью SSH и запустить клиентские средства для доступа к кластеру Hadoop в HDInsight.

## Добавление граничного узла в имеющийся кластер

В этом разделе описано, как использовать шаблон Resource Manager, чтобы добавить граничный узел в имеющийся кластер HDInsight. Шаблон Resource Manager можно найти в [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Шаблон Resource Manager вызывает скрипт действия сценария, расположенный по адресу https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Этот скрипт не выполняет никаких действий. Он необходим для демонстрации вызова действия скрипта с помощью шаблона Resource Manager.

**Добавление граничного узла в имеющийся кластер**

1. Создайте кластер HDInsight, если его еще нет. Дополнительные сведения см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Azure Resource Manager на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Настройте следующие свойства:

	- CLUSTERNAME: введите имя имеющегося кластера HDInsight.
	- EDGENODESIZE: выберите один из размеров виртуальной машины.
	- EDGENODEPREFIX: значение по умолчанию — **new**. Имя граничного узла по умолчанию — **new-edgenode**. Префикс можно настроить на портале. В шаблоне также можно настроить полное имя.


4. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.
5. В поле **Группа ресурсов** выберите группу ресурсов.
6. Щелкните **Просмотреть юридические условия**, а затем нажмите кнопку **Приобрести**.
7. Выберите **Закрепить на панели мониторинга** и нажмите кнопку **Создать**.

## Добавление граничного узла при создании кластера

В этом разделе описано, как использовать шаблон Resource Manager, чтобы создать кластер HDInsight с граничным узлом. Шаблон Resource Manager можно найти в общедоступном [контейнере хранилища BLOB-объектов Azure](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json). Шаблон Resource Manager вызывает скрипт действия сценария, расположенный по адресу https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Этот скрипт не выполняет никаких действий. Он необходим для демонстрации вызова действия скрипта с помощью шаблона Resource Manager.

**Добавление граничного узла в имеющийся кластер**

1. Создайте кластер HDInsight, если его еще нет. Дополнительные сведения см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Azure Resource Manager на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Настройте следующие свойства:
		
	- CLUSTERNAME: введите имя для нового кластера.
	- CLUSTERLOGINUSERNAME: введите имя пользователя HTTP для Hadoop. Имя по умолчанию — **admin**.
	- CLUSTERLOGINUSERNAME: введите пароль HTTP для Hadoop.
	- SSHUSERNAME: введите имя пользователя SSH. Имя по умолчанию — **sshuser**.
	- SSHPASSWORD: введите пароль пользователя SSH.
	- LOCATION: укажите расположение группы ресурсов, кластера и учетной записи хранения по умолчанию.
	- CLUSTERTYPE: значение по умолчанию — **hadoop**.
	- CLUSTERWORKERNODECOUNT: значение по умолчанию — 2.
	- EDGENODESIZE: выберите один из размеров виртуальной машины.
	- EDGENODEPREFIX: значение по умолчанию — **new**. Имя граничного узла по умолчанию — **new-edgenode**. Префикс можно настроить на портале. В шаблоне также можно настроить полное имя.

4. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.
5. В поле **Группа ресурсов** введите имя новой группы ресурсов.
6. Щелкните **Просмотреть юридические условия**, а затем нажмите кнопку **Приобрести**.
7. Выберите **Закрепить на панели мониторинга** и нажмите кнопку **Создать**.


## Доступ к граничному узлу

Конечная точка SSH граничного узла указывается в формате <имя граничного узла>.<имя кластера>-ssh.azurehdinsight.net:22. Например, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Граничный узел отображается в виде приложения на портале Azure. На портале представлены сведения для доступа к граничному узлу с помощью SSH.

**Проверка конечной точки SSH граничного узла**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Откройте кластер HDInsight с граничным узлом.
3. В колонке кластера щелкните **Приложения**. Затем вы увидите граничный узел. Его имя по умолчанию — **new-edgenode**.
4. Щелкните граничный узел. Отобразится конечная точка SSH.

**Использование Hive в граничном узле**

5. К граничному узлу можно подключиться с помощью SSH. Дополнительные сведения об использовании SSH см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](hdinsight-hadoop-linux-use-ssh-unix.md) или [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. После подключения к граничному узлу с помощью SSH откройте консоль Hive, используя следующую команду:

		hive
7. Чтобы показать таблицы Hive в кластере, выполните следующую команду:

		show tables;

## Удаление граничного узла

Граничный узел можно удалить на портале Azure.

**Доступ к граничному узлу**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Откройте кластер HDInsight с граничным узлом.
3. В колонке кластера щелкните **Приложения**. Появится список граничных узлов.
4. Щелкните правой кнопкой мыши граничный узел, который нужно удалить, и выберите пункт **Удалить**.
5. Нажмите кнопку **Да** для подтверждения.

## Дальнейшие действия

В этой статье вы узнали, как добавить граничный узел и как получить к нему доступ. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Установка приложений HDInsight](hdinsight-apps-install-applications.md) — узнайте, как устанавливать в кластер приложения HDInsight.
- [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md) — узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
- [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md) — узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
- [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight) — узнайте, как определить приложения HDInsight.
- [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md) — узнайте, как использовать действие скрипта для установки дополнительных приложений.
- [Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.

<!----HONumber=AcomDC_0914_2016-->