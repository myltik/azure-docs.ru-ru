<properties
	pageTitle="Использование песочницы Hadoop для ознакомления с Hadoop | Microsoft Azure"
	description="Чтобы начать ознакомление с экосистемой Hadoop, можно настроить на виртуальной машине Azure песочницу Hadoop с платформы Hortonworks. "
	keywords="эмулятор hadoop,песочница hadoop"
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="nitinme"/>

# Начало работы в экосистеме Hadoop с песочницей Hadoop на виртуальной машине

Узнайте, как настроить на виртуальной машине песочницу Hadoop с платформы Hortonworks, чтобы ознакомиться с экосистемой Hadoop. Песочница представляет собой локальную среду разработки для ознакомления с Hadoop, распределенной файловой системой Hadoop (HDFS) и отправкой заданий.

## Предварительные требования

* [Oracle VirtualBox](https://www.virtualbox.org/)

Если вы знакомы с Hadoop, вы можете начать использовать Hadoop в Azure, создав кластер HDInsight. Дополнительную информацию см. в статье [Руководство по Hadoop. Начало работы с Hadoop в HDInsight на платформе Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

## Скачивание и установка виртуальной машины

1. На веб-странице [http://hortonworks.com/downloads/#sandbox](http://hortonworks.com/downloads/#sandbox) выберите элемент __Download for VirtualBox__ (Файл загрузки для VirtualBox) для HDP 2.4 в песочнице Hortonworks. Перед началом скачивания вам будет предложено зарегистрироваться в Hortonworks.

    ![Изображение ссылки для скачивания песочницы Hortonworks для VirtualBox](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)

2. На той же самой веб-странице выберите __VirtualBox Install Guide__ (Руководство по установке VirtualBox) для HDP 2.4 в песочнице Hortonworks. После этого будет скачан PDF-файл, содержащий инструкции по установке для виртуальной машины.

    ![Просмотр руководства по установке](./media/hdinsight-hadoop-emulator-get-started/view-install-guide.png)

## Запуск виртуальной машины

1. Запустите VirtualBox, выберите Hortonworks Sandbox (Песочница Hortonworks), __Start__ (Запустить), а затем __Normal Start__ (Обычный запуск).

    ![Обычный запуск](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)

2. После завершения процесса загрузки в виртуальной машине отобразятся инструкции для входа. Откройте веб-браузер и перейдите по отображаемому URL-адресу (как правило, http://127.0.0.1:8888).

## Установка паролей

1. На __начальном шаге__ на странице песочницы Hortonworks выберите __View Advanced Options__ (Просмотр дополнительных параметров). Используйте сведения на этой странице, чтобы войти в песочницу с помощью SSH. Введите указанные имя и пароль.

    > [AZURE.NOTE] Если у вас не установлен клиент SSH, можно использовать веб-SSH, предоставляемый виртуальной машиной по адресу http://localhost:4200/__.

    При первом подключении с помощью SSH вам будет предложено изменить пароль для учетной записи root. Введите новый пароль, который будет использоваться при входе с помощью SSH в будущем.

2. После входа в систему введите следующую команду:

        ambari-admin-password-reset
    
    При появлении запроса укажите пароль для учетной записи администратора Ambari. Он будет использоваться при получении доступа к веб-интерфейсу Ambari.

## Использование команды Hive

1. Используя подключение SSH к песочнице, запустите оболочку Hive с помощью следующей команды:

        hive

2. После запуска оболочки просмотрите таблицы, представленные в песочнице, используя следующую команду:

        show tables;

3. Используйте следующую команду, чтобы извлечь 10 строк из таблицы `sample_07`:

        select * from sample_07 limit 10;

## Дальнейшие действия

* [Использование средств Azure Data Lake для Visual Studio с песочницей Hortonworks](hdinsight-hadoop-emulator-visual-studio.md)
* [Learning the ropes of the Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/) (Общие сведения о разработке песочницы Hortonworks)
* [Hadoop tutorial - Getting started with HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/) (Руководство по Hadoop. Начало работы с HDP)

<!---HONumber=AcomDC_0921_2016-->