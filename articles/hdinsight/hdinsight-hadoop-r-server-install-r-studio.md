<properties
	pageTitle="Установка RStudio с R Server в HDInsight (предварительная версия) | Microsoft Azure"
	description="Процедура установки RStudio с R Server в кластере HDInsight (предварительная версия)."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# Установка RStudio с R Server в HDInsight (предварительная версия)

Сегодня для R существует несколько интегрированных сред разработки (IDE), включая недавно заявленное корпорацией Майкрософт решение [R Tools for Visual Studio](https://www.visualstudio.com/ru-RU/features/rtvs-vs.aspx) (RTVS), семейство локальных и серверных инструментов от [RStudio](https://www.rstudio.com/products/rstudio-server/) или [StatET](http://www.walware.de/goto/statet) на базе Eclipse от Walware. Чаще всего в Linux используется [RStudio Server](https://www.rstudio.com/products/rstudio-server/) — IDE на основе браузера, предназначенная для удаленных клиентов. Установка RStudio Server на граничном узле кластера HDInsight Premium предоставляет доступ ко всем функциональным возможностям IDE для разработки и выполнения R-скриптов с использованием R Server в кластере и может значительно повысить производительность по сравнению со стандартным применением R-консоли.

В этой статье вы узнаете, как установить бесплатную версию RStudio Server для сообщества на граничном узле кластера с помощью пользовательского скрипта. Если вы хотите использовать коммерческую версию RStudio Server Pro, необходимо выполнить инструкции по установке из [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] Для процедур, рассматриваемых в этом документе, требуется сервер R Server в кластере HDInsight. Они не будут работать при использовании кластера HDInsight, где среда R была установлена с помощью [действия сценария "Установить R"](hdinsight-hadoop-r-scripts-linux.md).

## Предварительные требования

* Кластер Azure HDInsight, на котором установлен R Server. Инструкции см. в разделе [Приступая к работе с R Server в кластерах HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Клиент SSH. В дистрибутивах Linux и Unix и Macintosh OS X команда `ssh` входит в состав операционной системы. Для Windows мы рекомендуем [Cygwin](http://www.redhat.com/services/custom/cygwin/) (с [параметром OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU)) или [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).


## Установка RStudio в кластере с помощью пользовательского скрипта

1. Определите граничный узел кластера. Ниже указано соглашение об именовании головных и граничных узлов для кластера HDInsight с R Server.

	* Головной узел — `CLUSTERNAME-ssh.azurehdinsight.net`
	* Граничный узел — `R-Server.CLUSTERNAME-ssh.azurehdinsight.net`

2. Подключитесь к граничному узлу кластера по протоколу SSH, используя шаблон именования выше.
 
	* При подключении из клиента Linux см. статью [Подключение к кластеру HDInsight на основе Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
	* При подключении из клиента Windows см. статью [Подключение к кластеру HDInsight на основе Linux с использованием PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. После подключения используйте учетные данные привилегированного пользователя в кластере. В сеансе SSH используйте следующую команду:

		sudo su -

4. Скачайте пользовательский скрипт для установки RStudio. Используйте следующую команду:

		wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Измените разрешения для файла пользовательского скрипта и запустите скрипт. Используйте такие команды:

		chmod 755 InstallRStudio.sh
		./InstallRStudio.sh

6. Если при создании кластера HDInsight с R Server использовался пароль SSH, можно пропустить этот шаг и перейти к следующему. Если вместо него для создания кластера использовался ключ SSH, необходимо задать пароль для пользователя SSH. Он понадобится при подключении к RStudio. Выполните команды ниже. При появлении запроса на ввод **текущего пароля Kerberos** просто нажмите клавишу **ВВОД**. Обратите внимание, что `USERNAME` необходимо заменить именем пользователя SSH для кластера HDInsight.

		passwd USERNAME
		Current Kerberos password:
		New password:
		Retype new password:
		Current Kerberos password:
		
	При успешной установке пароля отобразится примерно следующее сообщение:

		passwd: password updated successfully


	Выйдите из сеанса SSH.

7. Создайте туннель SSH для кластера, сопоставив `localhost:8787` в кластере HDInsight с клиентским компьютером. Туннель SSH следует создать перед открытием нового сеанса браузера.

	* В клиенте Linux или Windows (используя [Cygwin](http://www.redhat.com/services/custom/cygwin/)) откройте сеанс терминала и введите следующую команду:

			ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
			
		Замените **ИМЯ\_ПОЛЬЗОВАТЕЛЯ** именем пользователя SSH для кластера HDInsight, а **ИМЯ\_КЛАСТЕРА** — именем кластера HDInsight. Вы также можете использовать ключ SSH вместо пароля, добавив `-i id_rsa_key`

	* Если вы используете клиент Windows и PuTTY, выполните следующие действия:

		1.  Откройте PuTTY и введите информацию о подключении. Если вы не знакомы с PuTTY, обратитесь к разделу [Использование SSH с Hadoop на основе Linux в HDInsight из Windows](hdinsight-hadoop-linux-use-ssh-windows.md), чтобы узнать, как пользоваться PuTTY с HDInsight.
		2.  В разделе **Категории** в левой части диалогового окна последовательно разверните **Подключение**, **SSH** и выберите **Туннели**.
		3.  Введите следующую информацию в форме **Параметры, управляющие перенаправлением портов SSH**:

			* **Порт источника** — порт на стороне клиента, трафик которого нужно перенаправлять. Например, **8787**.
			* **Назначение** — место назначения, которое следует сопоставить с локальным клиентским компьютером. Например, **localhost:8787**.

			![Создание туннеля SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Создание туннеля SSH")

		4. Щелкните **Добавить**, чтобы добавить параметры, а затем щелкните **Открыть**, чтобы открыть подключение SSH.
		5. При появлении запроса войдите на сервер. При этом будет установлен сеанс SSH и включен туннель.

8. Откройте браузер и введите следующий URL-адрес с учетом порта, введенного для туннеля.

		http://localhost:8787/ 

9. Появится запрос на ввод имени пользователя и пароля SSH для подключения к кластеру. Если при создании кластера использовался ключ SSH, необходимо ввести пароль, созданный на шаге 5.

	![Подключение к RStudio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Создание туннеля SSH")

10. Чтобы проверить успешность установки RStudio, можно запустить тестовый скрипт, выполняющий задания MapReduce и Spark в кластере на языке R. Вернитесь к консоли SSH и введите следующие команды, чтобы скачать тестовый скрипт для запуска в RStudio.

	* При создании кластера Hadoop на языке R используйте эту команду:
		
			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

	* При создании кластера Spark на языке R используйте эту команду:

			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. В RStudio появится скачанный тестовый скрипт. Дважды щелкните файл, чтобы открыть его, выделите содержимое файла и нажмите кнопку **Run** (Запустить). Результаты появятся в области **Console** (Консоль).
 
	![Проверка установки](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Проверка установки")

Еще один вариант — ввести `source(testhdi.r)` или `source(testhdi_spark.r)` для выполнения скрипта.

## Дополнительные материалы

- [Compute context options for R Server on HDInsight clusters (Параметры контекста вычислений для R Server в кластерах HDInsight)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure Storage options for R Server on HDInsight premium](hdinsight-hadoop-r-server-storage.md)


 

<!---HONumber=AcomDC_0921_2016-->