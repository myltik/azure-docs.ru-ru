<properties 
	pageTitle="Настройка виртуальной машины Azure для обработки и анализа данных" 
	description="Настройка виртуальной машины Azure для облачной среды обработки и анализа данных с сервером IPython." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mohabib;xibingao;bradsev" />

# Настройка виртуальной машины Azure для обработки и анализа данных

В этой статье объясняется, как подготовить и настроить виртуальную машину Azure так, чтобы она стала полноценной частью облачной среды обработки и анализа данных. Виртуальная машина Windows включает в себя вспомогательные средства IPython Notebook, обозреватель хранилищ Azure, AzCopy и другие служебные программы, полезные на проектах по обработке и анализу данных. Обозреватель хранилищ Azure и AzCopy, например, позволяют с легкостью передавать данные в хранилище BLOB-объектов Azure с локального компьютера и наоборот.

## <a name="create-vm"></a>Шаг 1. Создание виртуальной машины Azure общего назначения

Если у вас уже есть виртуальная машина Azure и осталось только настроить на ней сервер IPython Notebook, вы можете пропустить этот шаг и перейти сразу к шагу 2, [ Добавление конечной точки для IPython Notebook к существующей виртуальной машине](#add-endpoint). 
 
Прежде чем создавать в Azure виртуальную машину, определитесь с тем, какой у нее должен быть размер, чтобы успешно обрабатывать данные в рамках своих проектов. У небольших машин меньше памяти и ядер ЦП, чем у больших, но они менее дорогие. Сведения о ценах и типах машин см. на странице [Цены на виртуальные машины](http://azure.microsoft.com/pricing/details/virtual-machines/). 

1. Войдите в систему на странице https://manage.windowsazure.com и в левом нижнем углу нажмите кнопку **Создать**. Откроется новое окно. Последовательно выберите элементы **СРЕДА ВЫПОЛНЕНИЯ ПРИЛОЖЕНИЙ** -> **ВИРТУАЛЬНАЯ МАШИНА** -> **ИЗ ГАЛЕРЕИ**.

	![Create workspace][24]

2. Выберите один из образов: 

	* Центр обработки данных Windows Server 2012 R2; 
	* Режим Windows Server Essentials (Windows Server 2012 R2). 

	Затем, чтобы перейти на следующую страницу конфигурации, щелкните в правом нижнем углу стрелку вправо.
	
	![Create workspace][25]

3. Введите имя виртуальной машины, которую вы хотите создать, выберите размер машины, исходя из размера данных, которые машина будет обрабатывать, и желаемой мощности машины (объем памяти и количество ядер), введите имя пользователя и пароль для машины. Затем, чтобы перейти на следующую страницу конфигурацию, щелкните стрелку вправо.

	![Create workspace][26]

4. Выберите **РЕГИОН, ТЕРРИТОРИАЛЬНУЮ ГРУППУ, ВИРТУАЛЬНУЮ СЕТЬ**, содержащую **УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**, которую вы планируете использовать для этой виртуальной машины, а затем выберите эту учетную запись. Добавьте конечную точку внизу в поле **КОНЕЧНЫЕ ТОЧКИ**. Для этого укажите ее имя (в нашем случае это "IPython"). Вы можете выбрать любую строку в качестве **ИМЕНИ** конечной точки и любое **доступное** целое число от 0 до 65536 в качестве **ОБЩЕГО ПОРТА**. У **ЧАСТНОГО ПОРТА** должен быть номер **9999**. Пользователям **не следует** использовать общие порты, уже назначенные для служб Интернета. [Порты для служб Интернета](http://www.chebucto.ns.ca/~rakerman/port-table.html): в этой таблице приведен список портов, которые не следует использовать, так как они уже назначены. 

	![Create workspace][27]

	>[AZURE.NOTE] Если конечная точка добавляется на этом этапе, то шаг 2, [ Добавление конечной точки для IPython Notebook к существующей виртуальной машине](#add-endpoint), можно пропустить.

5. Нажмите кнопку с флажком, чтобы начать подготовку виртуальной машины. 

	![Create workspace][28]


Подготовка может занять 15-25 минут. Состояние созданной виртуальной машины должно быть таким: **Выполняется**.

![Create workspace][29]
	
## <a name="add-endpoint"></a>Шаг 2. Добавление конечной точки для IPython Notebook к существующей виртуальной машине

Если вы создали виртуальную машину, следуя инструкциям шага 1, то конечная точка для IPython Notebook уже создана и текущий шаг можно пропустить. 

Если виртуальная машина уже существует и нужно добавить конечную точку для сервера IPython Notebook, который вы установите на шаге 3, сначала войдите на портал управления Azure, выберите виртуальную машину и добавьте конечную точку для сервера IPython Notebook. Рисунок ниже - это снимок экрана, на котором показано состояние портала после того, как конечная точка для IPython Notebook была добавлена в виртуальную машину Windows. 

![Create workspace][17]

## <a name="run-commands"></a>Шаг 3. Установка IPython Notebook и других вспомогательных средств

Чтобы войти в созданную виртуальную машину Windows, используйте протокол удаленного рабочего стола (RDP). Подробные инструкции см. в статье [Как войти в виртуальную машину под управлением Windows Server](../virtual-machines-log-on-windows-server.md). Откройте **командную строку** (**не командное окно Powershell**) с правами администратора и выполните следующую команду.
 
    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

По завершении установки сервер IPython Notebook автоматически запустится из каталога *C:\Users\&#60;user name>\Documents\IPython Notebooks*.

Когда появится запрос, введите пароль для IPython Notebook и пароль администратора виртуальной машины. Это позволит серверу IPython Notebook на виртуальной машине работать как служба. 

## <a name="access"></a>Шаг 4. Доступ к IPython Notebook из веб-браузера
Чтобы попасть на сервер IPython Notebook, откройте веб-браузер и в адресной строке введите *https://&#60;virtual machine DNS name>:&#60;public port number>*, где *&#60;public port number>* - это номер порта, указанный при добавлении конечной точки IPython Notebook. Если в качестве номера общего порта вы указали *443*, то для подключения к серверу IPython Notebook не обязательно указывать номер порта в URL-адресе. Иначе номер порта (**&#60;public port number>*) нужно указывать обязательно. 

DNS-имя виртуальной машины (*&#60;virtual machine DNS name>*) вы можете найти на портале управления Azure. Войдя на портал управления, щелкните элемент **ВИРТУАЛЬНЫЕ МАШИНЫ**, выберите созданную машину, а затем щелкните **ПАНЕЛЬ МОНИТОРИНГА**. В открывшемся окне вы найдете DNS-имя.

![Create workspace][19]

Появится предупреждение с текстом _Ошибка в сертификате безопасности этого веб-сайта_ (Internet Explorer) или _Ваше подключение не является частным_ (Chrome) (см. рисунки ниже). Чтобы продолжить, щелкните **Продолжить открытие этого веб-узла (не рекомендуется)** (Internet Explorer) или **Дополнительно**, а затем - **Перейти к &#60;*DNS-имя*> (небезопасно)** (Chrome). После этого введите пароль, указанный ранее для доступа к IPython Notebook.

Internet Explorer:
![Create workspace][20]

Chrome:
![Create workspace][21]

Когда вы войдете на сервер IPython Notebook, в браузере отобразится каталог *DataScienceSamples*. В этом каталоге находятся образцы IPython Notebook, которые мы бесплатно предоставляем пользователям, чтобы помочь им с обработкой и анализом данных. Эти образцы извлекаются из [**репозитория Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) в виртуальные машины во время настройки сервера IPython Notebook. Мы обслуживаем и обновляем этот репозиторий на постоянной основе. В нем пользователи могут найти самые последние версии образцов IPython Notebook. 
![Create workspace][18]

## <a name="upload"></a>Шаг 5. Передача существующей записной книжки IPython Notebook с локального компьютера на сервер IPython Notebook

Вы можете с легкостью передать существующую записную книжку IPython Notebook с локального компьютера на виртуальную машину с сервером IPython Notebook. Войдя в IPython Notebook из веб-браузера, выберите **каталог**, в который будет передана записная книжка IPython Notebook. Затем выберите в **проводнике** IPYNB-файл, который нужно передать с локального компьютера, и перетащите этот файл в каталог IPython Notebook в веб-браузере. Чтобы отправить IPYNB-файл на сервер IPython Notebook, нажмите кнопку **Загрузить**. После этого другие люди смогут пользоваться этим файлом из своих веб-браузеров.

![Create workspace][22]

![Create workspace][23]


## <a name="shutdown"></a>Завершение работы и отмена распределения памяти для виртуальной машины, когда она не используется

За виртуальные машины Azure вы **платите только по факту использования**. Чтобы вам не выставляли счета за неиспользуемую виртуальную машину, она должна находиться в состоянии **Остановлено (освобождено)**.

> [AZURE.NOTE] Если отключить виртуальную машину средствами управления электропитанием Windows, она будет остановлена, но память для нее по-прежнему будет выделена. Чтобы избежать начисления оплаты, всегда останавливайте виртуальные машины из [портала управления Azure](http://manage.windowsazure.com/). Кроме того, вы можете остановить виртуальную машину из командной строки Powershell, вызвав команду **ShutdownRoleOperation** и указав для параметра PostShutdownAction значение StoppedDeallocated.

Вот как можно выключить виртуальную машину и отменить для нее распределение памяти:

1. Войдите на [портал управления Azure](http://manage.windowsazure.com/), используя свою учетную запись.  

2. На панели навигации слева выберите **ВИРТУАЛЬНЫЕ МАШИНЫ**.

3. В списке виртуальных машин выберите свою и перейдите на страницу **ПАНЕЛЬ МОНИТОРИНГА**.

4. В нижней части страницы нажмите кнопку **ЗАВЕРШЕНИЕ РАБОТЫ**. 

![VM Shutdown][15]

Распределение памяти для виртуальной машины будет отменено, но сама виртуальная машина останется. Вы можете перезапустить виртуальную машину в любое время на портале управления Azure.

## Виртуальная машина Azure готова к использованию: что дальше

Теперь вы можете использовать свою виртуальную машину для обработки и анализа данных. Кроме того, ее можно использовать в качестве сервера IPython Notebook для просмотра и обработки данных, а также для других задач, связанных с Машинным обучением Azure и обработкой и анализом данных в облаке. 

Следующие шаги по обработке и анализу данных указаны в статье [Учебное пособие: расширенная обработка данных в Azure](machine-learning-data-science-advanced-data-processing.md). Среди них: перемещение данных в HDInsight, их обработка и отбор для изучения в рамках Машинного обучения Azure и т. д.

* Сведения о том, как перемещать данные в HDInsight из хранилища BLOB-объектов Azure, см. в статье [Создание и загрузка данных в таблицы Hive из хранилища больших двоичных объектов Azure](machine-learning-data-science-hive-tables.md).
* Сведения об обработке данных в HDInsight с помощью запросов Hive см. в статье [Отправка запросов Hive в кластеры Hadoop под управлением службы HDInsight при обработке и анализе данных в облаке](machine-learning-data-science-process-hive-tables.md).
* Сведения об отборе данных в HDInsight см. в статье [Образец данных в таблицах Azure HDInsight Hive](machine-learning-data-science-sample-data-hive.md).



[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png




<!--HONumber=49--> 