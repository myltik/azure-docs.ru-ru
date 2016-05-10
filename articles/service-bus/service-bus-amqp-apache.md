<properties 
   pageTitle="Установка Apache Qpid Proton-C на виртуальной машине Linux | Microsoft Azure"
   description="Создание виртуальной машины CentOS Linux с использованием виртуальных машин Azure и процедура построения и установки библиотеки Apache Qpid Proton-C."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Установка Apache Qpid Proton-C на виртуальной машине Azure под управлением Linux

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

В этом разделе демонстрируется создание виртуальной машины CentOS Linux с использованием виртуальных машин Azure, а также загрузка, сборка и установка библиотеки Apache Qpid Proton-C с языковыми привязками Python и PHP. Выполнив эти действия, вы сможете воспользоваться примерами Python и PHP, приведенными в этом руководстве.

Первое действие выполняется на [классическом портале Azure][]. На следующем снимке экрана показано, как создается виртуальная машина CentOS с именем scott-centos:

![Proton на виртуальной машине Azure по управлением Linux][0]

После подготовки на портале отображаются следующие сведения:

![Proton на виртуальной машине Azure по управлением Linux][1]

Чтобы войти на компьютер, нужно знать порт конечной точки для SSH. Это значение можно получить на [классическом портале Azure][], выбрав вновь созданную виртуальную машину и открыв вкладку **Конечные точки**. На следующем снимке экрана показано, что открытым портом SSH для этого компьютера является порт 57146.

![Proton на виртуальной машине Azure по управлением Linux][2]

Теперь можно подключиться к компьютеру с помощью SSH. В этом примере используется средство PuTTY, что и показано на следующем снимке экрана.

![Proton на виртуальной машине Azure по управлением Linux][3]

Для приложений Python и PHP в этом примере используются клиентские библиотеки Proton в Apache. Эти библиотеки можно загрузить по адресу: [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Процедура установки зависимостей и сборки приложения Python описана в файле Readme, который входит в дистрибутив. Вот краткий перечень необходимых действий:

1.  Отредактируйте файл конфигурации (/etc/yum.conf) и добавьте комментарий к исключению для обновлений заголовков ядра (# exclude=kernel*). Это необходимо для установки компилятора gcc.

2.  Установите пакеты необходимых компонентов:

	```
	# required dependencies 
	yum install gcc cmake libuuid-devel
	
	# dependencies needed for ssl support
	yum install openssl-devel
	
	# dependencies needed for bindings
	yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
	
	# dependencies needed for python docs
	yum install epydoc
	```

1.  Загрузите библиотеку Proton:

	```
	[azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
	--2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
	Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
	Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 868226 (848K) [application/x-gzip]
	Saving to: ‘qpid-proton-0.9.tar.gz’
	
	qpid-proton-0.9.tar.gz                               
	
	100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
	
	2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
	```

1.  Извлеките код Proton из архива распространения:

	```
	tar xvfz qpid-proton-0.9.tar.gz
	```

1.  Постройте и установите код, выполнив следующие действия, взятые из файла Readme.

	```
	From the directory where you found this README file:	
	
	mkdir build cd build
			
	# Set the install prefix. You may need to adjust depending on your		
	# system.		
	cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
			
	# Omit the docs target if you do not wish to build or install		
	# documentation.		
	make all docs
			
	# Note that this step will require root privileges.		
	make install
	```

После выполнения этих действий Proton будет установлен на компьютере и готов к использованию.

## Дальнейшие действия

Хотите узнать больше? Перейдите по следующей ссылке:

- [Протокол AMQP служебной шины — обзор][]

[Протокол AMQP служебной шины — обзор]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[классическом портале Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0427_2016-->