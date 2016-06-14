<properties
   	pageTitle="Установка приложений Hadoop в кластере HDInsight | Microsoft Azure"
   	description="Инструкции по установке приложений HDInsight в кластере HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="06/07/2016"
   	ms.author="jgao"/>

# Установка пользовательских приложений HDInsight

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight под управлением Linux. Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами. Из этой статьи вы узнаете, как установить приложение HDInsight, которое не было опубликовано на портале Azure в HDInsight. Вы установите приложение [Hue](http://gethue.com/).

Другие статьи по этой теме:

- [Publish HDInsight applications into the Azure Marketplace](hdinsight-apps-publish-applications.md) (Публикация приложений в Azure Marketplace) — узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
- [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight) — сведения об определении приложений HDInsight.

 
## Предварительные требования

Если вы хотите установить приложение HDInsight в существующем кластере HDInsight, вы должны создать кластер HDInsight. Инструкции по созданию кластера см. в разделе [Создание кластера](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). Вы также можете установить приложения HDInsight во время создания кластера HDInsight.


## Установка приложений HDInsight

Приложения HDInsight можно устанавливать во время создания кластера или в уже существующем кластере HDInsight. Чтобы определить шаблоны ARM, см. статью [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight).

Далее перечислены файлы, необходимые для развертывания этого приложения (Hue).

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json) — шаблон ARM для установки приложения HDInsight. Инструкции по разработке собственного шаблона ARM см. в статье [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight).
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh) — действие сценария, вызываемое шаблоном ARM для настройки граничного узла. 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) — двоичный файл hue, вызываемый из hui-install\_v0.sh. 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) — двоичный файл hue, вызываемый из hui-install\_v0.sh. 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz) — пример веб-приложения (Tomcat), вызываемый из hui-install\_v0.sh.

**Установка приложения Hue в существующем кластере HDInsight**

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон ARM на портале Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Эта кнопка открывает шаблон ARM на портале Azure. Шаблон ARM находится по адресу [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue). Чтобы научиться создавать этот шаблон ARM, см. статью [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight).
    
2. В колонке **Параметры** заполните следующие поля.

    - **Имя кластера** — имя кластера, в котором вы хотите установить приложение. Это должен быть существующий кластер.
    
3. Нажмите кнопку **ОК**, чтобы сохранить параметры.
4. В колонке **Настраиваемое развертывание** укажите **группу ресурсов**. Группа ресурсов — это контейнер, в который входит кластер, зависимая учетная запись хранения и другие ресурсы. Необходимо использовать группу ресурсов, в которую входит кластер.
5. Щелкните **Условия использования**, а затем нажмите кнопку **Создать**.
6. Установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Создать**. Состояние установки можно наблюдать на плитке, закрепленной на панели мониторинга, и в уведомлении портала (щелкните значок колокольчика в верхней части портала). Установка приложения занимает около 10 минут.

**Установка приложения Hue во время создания кластера**

1. Щелкните следующее изображение, чтобы войти в Azure и открыть шаблон ARM на портале Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Эта кнопка открывает шаблон ARM на портале Azure. Шаблон ARM находится по адресу [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json). Чтобы научиться создавать этот шаблон ARM, см. статью [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight).

2. Выполните инструкции по созданию кластера и установите приложение Hue. Дополнительные сведения о создании кластеров HDInsight см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Вызывать шаблоны ARM можно с помощью портала Azure, а также [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-powershell) и [интерфейса командной строки Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-azure-cli).

## проверка установки

Вы можете просмотреть состояние приложения на портале Azure, чтобы проверить установку приложения. Кроме того, вы можете проверить появление правильных конечных точек HTTP и веб-страницу (если она есть).

**Вход на портал Hue**

1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню слева щелкните **Кластеры HDInsight**. Если меню не отображается, нажмите кнопку **Обзор**, а затем щелкните элемент **Кластеры HDInsight**.
3. Щелкните кластер, в котором установлено приложение.
4. В колонке **Параметры** в категории **Общие** щелкните **Приложения**. В колонке **Установленные приложения** вы увидите приложение **Hue**.
5. Выберите в списке приложение **Hue**, чтобы просмотреть его свойства.  
6. Щелкните ссылку на веб-страницу, чтобы проверить веб-сайт. Откройте конечную точку HTTP в браузере, чтобы проверить веб-интерфейс Hue. Откройте конечную точку SSH с помощью [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) или другого [SSH-клиента](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## Устранение неполадок, связанных с установкой

Состояние установки приложения можно наблюдать в уведомлении портала (щелкните значок колокольчика в верхней части портала).


Если произошел сбой установки приложения, вы можете увидеть сообщения об ошибках и информацию об отладке в трех местах.

- Приложения HDInsight: общие сведения об ошибке.

    Откройте кластер на портале и в колонке "Параметры" щелкните пункт "Приложения".

    ![приложения hdinsight приложение ошибка установки](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)

- Действие сценария HDInsight: если сообщение об ошибке приложений HDInsight указывает на сбой действия сценария, дополнительные сведения о сбое сценария отобразятся на панели действий сценария.

    В колонке "Параметры" выберите пункт "Действия сценария". Журнал действий сценария отображает сообщения об ошибках.

    ![приложения hdinsight ошибка действия сценария](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
    
- Веб-интерфейс Ambari: если сценарий установки был причиной сбоя, используйте веб-интерфейс Ambari, чтобы просмотреть полные журналы сценариев установки.

    Дополнительные сведения см. в разделе [Устранение неполадок](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting).

## Удаление приложений HDInsight

Есть несколько способов удалить приложения HDInsight.

### С помощью портала

**Удаление приложения с помощью портала**

1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню слева щелкните **Кластеры HDInsight**. Если меню не отображается, нажмите кнопку **Обзор**, а затем щелкните элемент **Кластеры HDInsight**.
3. Щелкните кластер, в котором установлено приложение.
4. В колонке **Параметры** в категории **Общие** щелкните **Приложения**. Вы увидите список установленных приложений. В нашем примере в колонке **Установленные приложения** отображается приложение **Hue**.
5. Щелкните правой кнопкой мыши приложение, которое нужно удалить, и выберите пункт **Удалить**.
6. Нажмите кнопку **Да** для подтверждения.

На портале можно также удалить кластер или группу ресурсов, которая содержит приложение.

### Использование Azure PowerShell

С помощью PowerShell можно удалить кластер или группу ресурсов. Подробные сведения об удалении кластеров с помощью Azure PowerShell см. [здесь](hdinsight-administer-use-powershell.md#delete-clusters).

### Использование интерфейса командной строки Azure

С помощью интерфейса командной строки можно удалить кластер или группу ресурсов. Подробные сведения об удалении кластеров с помощью интерфейса командной строки Azure см. [здесь](hdinsight-administer-use-command-line.md#delete-clusters).


## Дальнейшие действия

- [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight) — узнайте, как разрабатывать шаблоны ARM для развертывания приложений HDInsight.
- [Publish HDInsight applications into the Azure Marketplace](hdinsight-apps-publish-applications.md) (Публикация приложений в Azure Marketplace) — узнайте, как публиковать пользовательские приложения HDInsight в Azure Marketplace.
- [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md) — узнайте, как использовать действие скрипта для установки дополнительных приложений.
- [Создание кластеров Hadoop под управлением Linux в HDInsight с помощью шаблонов ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны ARM для создания кластеров HDInsight.

<!---HONumber=AcomDC_0608_2016-->