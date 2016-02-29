<properties
	pageTitle="10 задач, которые можно выполнить в виртуальной машине для обработки и анализа данных | Microsoft Azure"
	description="Выполните различные задачи по изучению и моделированию данных в виртуальной машине для анализа и обработки данных."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/13/2016"
	ms.author="gokuma;weig;bradsev" />

# 10 задач, которые можно выполнить в виртуальной машине для обработки и анализа данных 

Виртуальная машина для обработки и анализа данных Майкрософт (DSVM) — это мощная среда разработки научных данных, позволяющая выполнять различные задачи по исследованию и моделированию данных.  Среда поставляется в уже готовом виде и входит в пакет нескольких распространенных средств анализа данных, которые помогают легко и быстро начать анализ. DSVM тесно взаимодействует со многими службами Azure и может читать и обрабатывать данные, уже хранящиеся в Azure в хранилище данных SQL Azure, озере данных Azure, хранилище Azure или DocumentDB. Она также может использовать инструменты аналитики, такие как машинное обучение Azure и фабрика данных Azure.


В этой статье приводится пошаговое руководство по использованию DSVM для выполнения различных задач обработки и анализа данных, а также взаимодействия с другими службами Azure. Вот некоторые задачи, которые можно выполнить в DSVM:

1. Просмотр данных и локальная разработка моделей на DSVM с помощью Microsoft R Server, Python
2. Использование записной книжки Jupyter для проведения экспериментов с данными в браузере с помощью Python 2, Python 3, Microsoft R (готовой корпоративной версии R для обеспечения улучшенной масштабируемости и производительности)
3. Ввод моделей, построенных с помощью R и Python, в эксплуатацию в службе машинного обучения Azure, чтобы клиентские приложения могли обращаться к ним с использованием простого интерфейса веб-служб
4. Администрирование ресурсов Azure с помощью портала Azure или PowerShell 
5. Увеличение объема хранилища и предоставление всем участникам команды общего доступа к большим наборам данных и коду за счет создания файлового хранилища Azure в виде подключаемого диска к DSVM 
6. Совместное использование кода с коллегами с помощью Github и доступ к репозиторию с помощью предварительно установленных клиентов Git — Git Bash, Git GUI
7. Доступ к различным службам данных и службам аналитики Azure, таким как хранилище BLOB-объектов, озеро данных Azure, Azure HDInsight (Hadoop), Azure DocumentDB, хранилище данных SQL Azure и базы данных Azure
8. Создание отчетов и панелей мониторинга с помощью службы Power BI Desktop, предустановленной на DSVM, и их развертывание в облаке
9. Динамическое масштабирование DSVM в соответствии с требованиями проекта 
10. Установка дополнительных инструментов на виртуальной машине

**Необходимые компоненты**. Вам понадобится подписка Azure. Вы можете зарегистрироваться для получения бесплатной пробной версии [здесь](https://azure.microsoft.com/free/). Инструкции по подготовке виртуальной машины для обработки и анализа данных на портале Azure доступны в статье [Создание виртуальной машины](https://ms.portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## 1\. Просмотр данных и разработка моделей с помощью Microsoft R Server или Python

Используя такие языки, как R и Python, анализ данных можно выполнять непосредственно в DSVM.

При работе с R можно использовать IDE с именем "Revolution R Enterprise 8.0", которая находится в меню "Пуск" или на рабочем столе. Корпорация Майкрософт предоставила дополнительные библиотеки поверх R с открытым исходным кодом или CRAN-R для осуществления масштабируемой аналитики и анализа данных, размер которых превышает разрешенный объем памяти, путем выполнения параллельной и фрагментарной обработки данных. Ниже приведен снимок экрана использования интегрированной среды разработки R.

![R IDE](./media/machine-learning-data-science-vm-do-ten-things/RevoIDE.png)

Для Python можно использовать такую интегрированную среду разработки, как Visual Studio Community Edition, в которой предустановлено расширение Python Tools for Visual Studio (PTVS). По умолчанию в PTVS настроена только базовая версия Python 2.7 (без аналитических библиотек, таких как SciKit, Pandas). Чтобы включить Anaconda Python 2.7 и 3.5, необходимо выполнить приведенные далее действия.

* Создайте настраиваемые среды для каждой версии, последовательно выбрав "Инструменты" -> "Инструменты Python" -> "Среды Python" и затем нажав кнопку "+ Настраиваемые" в Visual Studio 2015 Community Edition.
* Введите описание и задайте пути префиксов сред — c:\\anaconda для Anaconda Python 2.7 ИЛИ c:\\anaconda\\envs\\py35 для Anaconda Python 3.5. 
* Щелкните **Автообнаружение**, а затем **Применить**, чтобы сохранить среду. 

Вот как выглядит установка настраиваемой среды в Visual Studio.

![Установка PTVS](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

Дополнительные сведения о создании сред Python см. в [документации к PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it).
  
Теперь вы можете открыть проект и приступить к работе!

## 2\. Использование записной книжки Jupyter для изучения и моделирования данных с помощью Python или R

Записная книжка Jupyter представляет собой мощную среду IDE с поддержкой браузера для изучения и моделирования данных. Можно использовать Python 2, Python 3 или R (с открытым исходным кодом и Microsoft R Server). Чтобы запустить записную книжку Jupyter, щелкните значок меню "Пуск" или значок на рабочем столе с именем **Записная книжка Jupyter**. Чтобы получить доступ к записной книжке Jupiter с DSVM, перейдите по адресу "https://localhost:9999/". Если будет предложено ввести пароль, следуйте инструкциям по созданию надежного пароля, приведенным на [странице документации к DSVM](machine-learning-data-science-provision-vm.md/#how-to-create-a-strong-password-on-the-jupyter-notebook-server).

ПОДЛЕЖИТ УТОЧНЕНИЮ: СНИМОК ЭКРАНА

В записной книжке вы увидите каталог, содержащий несколько примеров записных книжек, которые предварительно упакованы в DSVM. Щелкните записную книжку, чтобы просмотреть код. Чтобы выполнить каждую ячейку, нажимайте клавиши **SHIFT + ВВОД**. Чтобы выполнить всю записную книжку, щелкните **Ячейка** -> **Выполнить**.

Чтобы создать записную книжку, щелкните значок Jupyter (в левом верхнем углу), нажмите кнопку **Создать** справа и выберите язык записной книжки (также известный как ядра). В настоящее время поддерживается Python 2.7, Python 3.5 и R. Ядро R поддерживает программирование на R с открытым исходным кодом и на корпоративной масштабируемой платформе Microsoft R Server. Выполнив вход в записную книжку, вы можете изучать данные, создавать модели и тестировать их с использованием нужных библиотек.


## 3\. Создание моделей с помощью R и Python и ввод их в эксплуатацию в службе машинного обучения Azure

После создания и проверки модели обычно следует этап развертывания этой модели в рабочей среде. Это позволит клиентским приложениям вызывать прогнозы модели в режиме реального времени или пакетном режиме. Служба машинного обучения Azure предлагает механизм ввода в эксплуатацию модели, созданной на языке R или Python. После ввода модели в эксплуатацию в службе машинного обучения Azure становится доступна веб-служба, позволяющая клиентам выполнять вызовы REST, которые передают входные параметры и получают прогнозы из модели в виде выходных данных. Если вы не еще зарегистрировались для использования службы машинного обучения Azure, вы можете получить бесплатный гостевой 8-часовой доступ или бесплатную рабочую область. Для этого на домашней странице [Студии машинного обучения Azure](https://studio.azureml.net/) щелкните "Приступая к работе".

### Построение и ввод в эксплуатацию моделей, созданных на языке Python  

Отправьте в Jupyter записную книжку "IrisClassifierPyMLWebService". Далее приводится простая модель, созданная на языке Python с помощью библиотеки SciKit-learn, находящейся в записной книжке.
  
	python
	#IRIS classification
	from sklearn import datasets
	from sklearn import svm
	clf = svm.SVC()
	iris = datasets.load_iris()
	X, y = iris.data, iris.target
	clf.fit(X, y) 
 
Метод, используемый для развертывания моделей Python в службе машинного обучения Azure, предназначен для обертывания прогноза модели в функцию и ее параметризации с помощью предоставляемых библиотекой службы машинного обучения Azure атрибутов, которые обозначают ИД рабочей области, ключ API, входные параметры и возвращаемые параметры службы машинного обучения.

	python
	from azureml import services
	@services.publish(workspaceid, auth_token)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(int) #0, or 1, or 2
	def predictIris(sep_l, sep_w, pet_l, pet_w):
 	inputArray = [sep_l, sep_w, pet_l, pet_w]
	return clf.predict(inputArray)

Теперь клиент может выполнять вызовы веб-службы. Существуют удобные оболочки для создания запросов REST API. Ниже приведен пример кода использования веб-службы.


 	python
	# Consume through web service URL and keys
	from azureml import services
	@services.service(url, api_key)
	@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
	@services.returns(float)
	def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

	IrisPredictor(3,2,3,4)


ПРИМЕЧАНИЕ. В настоящее время библиотека службы машинного обучения AzureML поддерживается только в Python 2.7.

### Создание и ввод в эксплуатацию моделей R

Процедура развертывания моделей R, построенных в виртуальной машине DSVM или в другом компоненте службы машинного обучения Azure, аналогична действиям, выполняемым для Python. Сначала создайте файл settings.json, как показано ниже, чтобы указать идентификатор рабочей области и маркер проверки подлинности. Затем напишите оболочку для функции прогнозирования модели. После этого можно вызвать ```publishWebService``` в библиотеке службы машинного обучения Azure, передав оболочку функции. Ниже приведен фрагмент кода, который можно использовать для публикации модели как веб-службы в службе машинного обучения Azure.

#### Файл Settings.json:

	json
	{"workspace":{
	"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
	"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
	}}


#### Создание модели на языке R и ее публикация в службе машинного обучения Azure

	R
	library(AzureML)
	ws <- workspace(config="~/.azureml/settings.json")

	if(!require("lme4")) install.packages("lme4")
	library(lme4)
	set.seed(1)
	train <- sleepstudy[sample(nrow(sleepstudy), 120),]
	m <- lm(Reaction ~ Days + Subject, data = train)
 
	# Define a prediction function to publish based on the model:
	sleepyPredict <- function(newdata){
  		predict(m, newdata=newdata)
	}
 
	ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### Использование модели, развернутой в службе машинного обучения Azure

Для использования модели из клиентского приложения мы используем библиотеку службы машинного обучения Azure, чтобы найти опубликованную веб-службу по имени с помощью вызова API `services` для определения конечной точки. Затем нужно просто вызвать функцию `consume` и передать блок данных для прогноза. Приведенный ниже код позволяет использовать модель, опубликованную как веб-службу машинного обучения Azure.


	R
	library(AzureML)
	library(lme4)
	ws <- workspace(config="~/.azureml/settings.json")

	s <-  services(ws, name = "sleepy lm")
	s <- tail(s, 1) # use the last published function, in case of duplicate function names

	ep <- endpoints(ws, s)

	# OK, try this out, and compare with raw data
	ans = consume(ep, sleepstudy)$ans


## 4\. Администрирование ресурсов Azure с помощью портала Azure или PowerShell

DSVM позволяет не только создавать аналитическое решение локально на виртуальной машине, но и предоставляет доступ к службам в облаке Azure корпорации Майкрософт. Azure предоставляет несколько вычислительных служб, служб хранения данных и служб аналитики данных, а также ряд других служб, которые можно администрировать и к которым можно обращаться с DSVM.

Для администрирования подписки и облачных ресурсов Azure можно воспользоваться браузером и перейти на [портал Azure](portal.azure.com). Кроме того, Azure PowerShell позволяет осуществлять администрирование подписки и ресурсов Azure с помощью сценариев. Azure PowerShell можно запустить с помощью ярлыка на рабочем столе или выбрать в меню "Пуск" компонент "Microsoft Azure PowerShell". Дополнительные сведения об администрировании подписки и ресурсов Azure с помощью сценариев Windows PowerShell см. в [документации к Microsoft Azure PowerShell](../powershell-azure-resource-manager.md).


## 5\. Увеличение объема хранилища за счет общей файловой системы

Специалисты по обработке и анализу данных могут использовать большие наборы данных, код и т. д. вместе с участниками группы. В самой виртуальной машине DSVM доступно около 70 ГБ свободного места. Чтобы увеличить объем хранилища, можно использовать службу файлов Azure и подключить ее к DSVM или получить к ней доступ через REST API. Максимальный объем общей папки службы файлов Azure равен 5 ТБ, а максимальный размер отдельного файла составляет 1 ТБ.

Создать общую папку службы файлов Azure можно с помощью Azure PowerShell. Далее приведен сценарий, выполняемый в Azure PowerShell для создания общей папки службы файлов Azure.

	# Authenticate to Azure. 
	Login-AzureRmAccount
	# Select your subscription
	Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
	# Create a new resource group. 
	New-AzureRmResourceGroup -Name <dsvmdatarg>
	# Create a new storage account. You can reuse existing storage account if you wish. 
	New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
	# Set your current working storage account
	Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>
	
	# Create a Azure File Service Share
	$s = New-AzureStorageShare <<teamsharename>>
	# Create a directory under the FIle share. You can give it any name
	New-AzureStorageDirectory -Share $s -Path <directory name>
	# List the share to confirm that everything worked
	Get-AzureStorageFile -Share $s


Созданную общую папку Azure можно подключить к любой виртуальной машине в Azure. Настоятельно рекомендуется, чтобы виртуальная машина находилась в одном центре обработки данных Azure с учетной записью хранения. Это необходимо, чтобы исключить задержки и избежать платы за передачу данных. Далее приведены команды для подключения диска к DSVM, которые выполняются в Azure PowerShell.


	# Get storage key of the storage account that has the Azure file share from Azurer portal. Store it securely on the VM to avoid prompted in next command.
	cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>
	
	# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
	net use z:  \<mydatadisk>.file.core.windows.net<<teamsharename>>


Теперь к этому диску можно обращаться как к обычному диску на виртуальной машине.

## 6\. Совместное использование кода с коллегами с помощью Github 

Github — это репозиторий кодов, в котором находится множество примеров кодов и источников для различных средств на базе разных технологий, совместно используемых участниками сообщества разработчиков. Git выступает в качестве технологии для отслеживания и хранения версий файлов кода. Github также является платформой для создания пользовательских репозиториев для хранения общего кода и документации группы, реализации системы управления версиями и определения того, какие пользователи получат доступ к просмотру и разработке кода. Дополнительные сведения об использовании Git см. на [страницах справки Github](https://help.github.com/). Github можно использовать как вариант совместной работы с участниками группы, реализации разработанного сообществом кода и возврата кода обратно в сообщество.

DSVM уже поставляется с набором клиентских средств, поддерживающих доступ к репозиторию Github как через командную строку, так и через графический интерфейс пользователя. Средство командной строки для работы с Git и Github называется ```git-bash```. Среда Visual Studio, установленная на виртуальной машине DSVM, имеет расширения Git. Значки для запуска этих средств находятся в меню "Пуск" и на рабочем столе.

Для скачивания кода из репозитория Github служит команда ```git clone```. Например, чтобы скачать репозиторий обработки и анализа данных, опубликованный корпорацией Майкрософт в текущем каталоге, выполните указанную ниже команду после входа в ```git-bash```.

	git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

В Visual Studio можно выполнить ту же операцию клонирования. На снимке экрана ниже приведены данные о доступе к средствам Git и Github в Visual Studio.

![Git в Visual Studio](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)


Дополнительные сведения об использовании Git для работы с репозиторием Github доступны в ряде ресурсов на сайте github.com. Возможно, полезным справочным документом для вас может стать эта [памятка](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf).


## 7\. Доступ к различным службам данных и службам аналитики Azure

### BLOB-объект Azure

**Предварительные требования**

- **Создайте учетную запись хранения BLOB-объектов Azure на [портале Azure](http://portal.azure.com).**

![Create\_Azure\_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)


- ****Убедитесь, что предварительно установленное средство AzCopy, находящееся в каталоге ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```, добавлено в переменную среды. Дополнительные сведения об AzCopy см. в [документации к AzCopy](../storage/storage-use-azcopy.md).


- **Запустите обозреватель хранилищ Azure [отсюда](https://azurestorageexplorer.codeplex.com/).**

![AzureStorageExplorer\_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)

**Перемещение данных из виртуальной машины в BLOB-объект Azure: AzCopy**

Для перемещения данных между локальным файлом и хранилищем BLOB-объектов можно использовать AzCopy в командной строке или PowerShell: `AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt`

Замените **C:\\myfolder** на путь к расположению, где хранится файл, **mystorageaccount** на имя учетной записи хранилища BLOB-объектов, **mycontainer** на имя контейнера, а значение в поле **Ключ учетной записи хранения** на ключ доступа к хранилищу BLOB-объектов. Ваши учетные данные учетной записи хранения можно найти на [портале Azure](http://portal.azure.com).

![StorageAccountCredential\_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

Выполните команду AzCopy в PowerShell или в командной строке. Далее приведен пример использования команды AzCopy.


	# Copy *.sql from local machine to a Azure Blob
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql
	
	# Copy back all files from Azure Blob container to Local machine
	
	"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
	


Вскоре после выполнения команды AzCopy для копирования в BLOB-объект файл появится в обозревателе хранилищ Azure.

![AzCopy\_run\_finshed\_Storage\_Explorer\_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)


**Перемещение данных из виртуальной машины в BLOB-объект Azure: обозреватель хранилищ Azure**

Отправить данные из локального файла в виртуальной машине можно также с помощью обозревателя хранилищ Azure.

![](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_upload_v2.png)


**Чтение данных из BLOB-объекта Azure: модуль чтения AML**

Для чтения данных из BLOB-объекта в студии машинного обучения можно использовать **модуль чтения**.

![AML\_ReaderBlob\_Module\_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)


**Чтение данных из BLOB-объекта Azure: Python ODBC**

Для чтения данных непосредственно из BLOB-объекта в IPython Notebook **Структурирование данных Нью-Йорка с помощью IPython Notebook IPython Notebook и хранилища BLOB-объектов Azure** можно использовать пакет **pyodbc**.

Сначала импортируйте необходимые пакеты.

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage import BlobService
	import tables
	import time
	import zipfile
	import random

Затем подключите учетные данные BLOB-объекта Azure и считайте данные из BLOB-объекта.

    CONTAINERNAME = 'xxx'
	STORAGEACCOUNTNAME = 'xxxx'
	STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
	BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
	localfilename = 'trip_data_1.csv'
	LOCALDIRECTORY = os.getcwd()
	LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

Вы увидите, что данные считываются в виде блока данных, как показано ниже.

![IPNB\_data\_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)


### Озеро данных Azure

**Предварительные требования**

- Создайте аналитику озера данных Azure на [портале Azure](http://portal.azure.com).

![Azure\_Data\_Lake\_Create\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)


- **Средства озера данных Azure** в **Visual Studio**, доступные по этой [ссылке](https://www.microsoft.com/download/details.aspx?id=49504), уже установлены в выпуске Visual Studio Community на виртуальной машине. После запуска Visual Studio и входа в подписку Azure вы увидите свою учетную запись аналитики данных Azure и хранилище в левой панели Visual Studio. 

![Azure\_Data\_Lake\_PlugIn\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)


- Установите **шлюз управления данными** согласно инструкциям в этом [документе](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

![Azure\_Data\_Gateway\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Gateway_v2.PNG)


- Предоставьте шлюзу данных Azure общий доступ к папке, где хранятся данные.

![Share\_Folder](./media/machine-learning-data-science-vm-do-ten-things/Share_Folder.PNG)


**Перемещение данных из виртуальной машины в озеро данных: обозреватель озера данных Azure**

**Обозреватель озера данных Azure** можно использовать для отправки данных из локальных файлов в виртуальной машине в хранилище озера данных.

![Azure\_Data\_Lake\_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)


**Перемещение данных из виртуальной машины в озеро данных: фабрика данных Azure**

Фабрика данных Azure — это служба для создания, планирования и контроля конвейеров данных. Фабрику данных Azure можно использовать для перемещения данных между различными хранилищами. Создайте [фабрику данных Azure](https://azure.microsoft.com/services/data-factory/) на [портале Azure](http://portal.azure.com).

![Azure\_Data\_Factory\_Create](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_Create.PNG)

После создания фабрики вы можете формировать конвейеры для перемещения данных между различными хранилищами. В разделе **Создание и развертывание** можно указать наборы данных для передачи и настроить конвейеры.

![Azure\_Data\_Factory\_Overview\_v4](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_Overview_v4.PNG)


Ниже приведены основные действия по перемещению данных из виртуальной машины в озеро данных Azure. Сведения о перемещении данных с помощью фабрики данных Azure можно найти [здесь](../data-factory/data-factory-data-movement-activities.md). В виртуальной машине для обработки и анализа данных будут находиться перечисленные ниже JSON-файлы.

1. **Создание связанных служб**
	- Щелкните **Новое хранилище данных**, выберите **Хранилище озера данных Azure**, укажите свои учетные данные и параметры в JSON-файле.
	- Щелкните **Новое хранилище данных**, **Файловая система**, укажите свои учетные данные и параметры в JSON-файле.
2. **Создание наборов данных**
	- Щелкните **Новый набор данных**, выберите **Озеро данных Azure**, укажите имя **связанной службы** и **путь к папке** в JSON-файле.
	- Щелкните **Новый набор данных**, выберите **Локальный файл**, укажите **схему набора данных**, имя **связанной службы**, **имя файла** и **путь к папке** в JSON-файле.
3. **Создание конвейеров**
	- Щелкните **Новый конвейер**, укажите **наборы входных и выходных данных**, **тип действия** и т. д. в JSON-файле. 
4. **Создание шлюзов данных**
	- Зарегистрируйте ключ шлюза и убедитесь, что регистрация находится в состоянии **Зарегистрировано** и **Запущено**.
	- Щелкните **Новый шлюз данных**, укажите **имя шлюза данных**. Раздел **Настройка** будет заполнен автоматически, если шлюз данных правильно установлен на предыдущих шагах.

![Azure\_Data\_Gateway\_part2\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Gateway_part2_v2.png)

![Azure\_Data\_Factory\_Template](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_Template.PNG)

![Azure\_Data\_Factory\_Template\_Json\_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_Template_Json_v2.PNG)

В шагах выше использовались следующие JSON-файлы:

**Связанные службы: AzureDataLakeStoreLinkedService**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "description": "",
	        "hubName": "weigadf_hub",
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://cdspkona.azuredatalakestore.net/webhdfs/v1",
	            "authorization": "**********",
	            "sessionId": "**********",
	            "subscriptionId": "49bb74df-a9b8-4275-9439-198b33ae0f5f",
	            "resourceGroupName": "weiguodsvn"
	        }
	    }
	}


**Связанные службы: OnPremisesFileServerLinkedService**

	{
	    "name": "OnPremisesFileServerLinkedService",
	    "properties": {
	        "description": "dsvm",
	        "hubName": "weigadf_hub",
	        "type": "OnPremisesFileServer",
	        "typeProperties": {
	            "host": "localhost",
	            "gatewayName": "weiggateway",
	            "userId": "weiguo",
	            "password": "**********"
	        }
	    }
	}


**Наборы данных: OnPremisesFile**

	{
	    "name": "OnPremisesFile",
	    "properties": {
	        "published": false,
	        "type": "FileShare",
	        "linkedServiceName": "OnPremisesFileServerLinkedService",
	        "typeProperties": {
	            "folderPath": "\\\dsvmjanc1ssd\\share"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


**Наборы данных: weiglakenew1**

	{
	    "name": "Datalakenew",
	    "properties": {
	        "structure": [
	            {
	                "name": "medallion",
	                "type": "String"
	            },
	            {
	                "name": "hack_license",
	                "type": "String"
	            },
	            {
	                "name": "vendor_id",
	                "type": "String"
	            },
	            {
	                "name": "rate_code",
	                "type": "String"
	            },
	            {
	                "name": "store_and_fwd_flag",
	                "type": "String"
	            },
	            {
	                "name": "pickup_datetime",
	                "type": "Datetime"
	            },
	            {
	                "name": "dropoff_datetime",
	                "type": "Datetime"
	            },
	            {
	                "name": "passenger_count",
	                "type": "Double"
	            },
	            {
	                "name": "trip_time_in_secs",
	                "type": "Decimal"
	            },
	            {
	                "name": "trip_distance",
	                "type": "Decimal"
	            },
	            {
	                "name": "pickup_longitude",
	                "type": "String"
	            },
	            {
	                "name": "pickup_latitude",
	                "type": "String"
	            },
	            {
	                "name": "dropoff_longitude",
	                "type": "String"
	            },
	            {
	                "name": "dropoff_latitude",
	                "type": "String"
	            }
	        ],
	        "published": false,
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "fileName": "UploadedFromVM_success.CSV",
	            "folderPath": "cdsp-data/nyctaxi_weig/"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**Наборы данных: vmtodatalake\_tripdata**

	{
	    "name": "vmtodatalake_tripdata",
	    "properties": {
	        "description": "vmtodatalake",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "AzureDataLakeStoreSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "OnPremisesFile"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "weiglakenew1"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "AzureBlobtoDataLake",
	                "description": "Copy Activity"
	            }
	        ],
	        "start": "2016-02-01T00:00:00Z",
	        "end": "2016-02-01T01:00:00Z",
	        "isPaused": false,
	        "hubName": "weigadf_hub",
	        "pipelineMode": "Scheduled"
	    }
	}


**Шлюзы данных: weiggateway**

	{
	    "name": "VMtoLakegateway",
	    "properties": {
	        "description": "",
	        "hostServiceUri": "https://dsvmjanc1ssd:8050/HostServiceRemote.svc/",
	        "dataFactoryName": "weigadf",
	        "status": "Online",
	        "versionStatus": "UpToDate",
	        "version": "1.9.5865.2",
	        "registerTime": "2016-01-28T20:21:51.2375545Z",
	        "lastConnectTime": "2016-02-12T00:06:55.3445063Z"
	    }
	}



Созданный конвейер можно посмотреть в разделе **Схема** на панели мониторинга фабрики данных Azure.


![](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_Find_Diagram.PNG)

Щелкните раздел **Схема**, чтобы увидеть конвейер.

![](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_PipeLine_Diagram.PNG)

Для наблюдения за конвейером последовательно выберите **Содержимое**->**Наборы данных**->**Мониторинг**.

![](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_Monitor_v2.PNG)


Чтобы проверить, перемещены ли данные в озеро данных Azure, воспользуйтесь **обозревателем озера данных Azure** в **Visual Studio**.


![](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Factory_Monitor_inVS.PNG)


**Чтение данных из BLOB-объекта Azure в озеро данных: U-SQL**

Если данные хранятся в хранилище BLOB-объектов Azure, их можно считывать непосредственно из BLOB-объекта хранилища Azure в запросе U-SQL. Перед построением запроса U-SQL убедитесь, что учетная запись хранения BLOB-объектов связана с озером данных Azure. На **портале Azure** найдите панель мониторинга аналитики озера данных Azure, щелкните **Добавить источник данных**, в качестве типа хранилища выберите **Хранилище Azure** и укажите имя и ключ учетной записи хранения Azure. Затем вы сможете ссылаться на данные, хранящиеся в учетной записи хранения.

![](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)


В Visual Studio можно читать данные из BLOB-объектов, выполнять обработку данных, проектировать характеристики и выводить итоговые данные в озеро данных Azure или хранилище BLOB-объектов Azure. Ссылаясь на данные в хранилище BLOB-объектов, используйте ****wasb://**, ссылаясь на данные в озере данных Azure, используйте ****swbhdfs://**.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

В Visual Studio можно использовать следующие запросы U-SQL:

	@a =
	    EXTRACT medallion string,
	            hack_license string,
	            vendor_id string,
	            rate_code string,
	            store_and_fwd_flag string,
	            pickup_datetime string,
	            dropoff_datetime string,
	            passenger_count int,
	            trip_time_in_secs double,
	            trip_distance double,
	            pickup_longitude string,
	            pickup_latitude string,
	            dropoff_longitude string,
	            dropoff_latitude string
	
	    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
	    USING Extractors.Csv();
	
	@b = 
	    SELECT vendor_id, 
	    COUNT(medallion) AS cnt_medallion,
	    SUM(passenger_count) AS cnt_passenger,
	    AVG(trip_distance) AS avg_trip_dist,
	    MIN(trip_distance) AS min_trip_dist,
	    MAX(trip_distance) AS max_trip_dist,
	    AVG(trip_time_in_secs) AS avg_trip_time
	    FROM @a
	    GROUP BY vendor_id;
	
	OUTPUT @b   
	TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
	USING Outputters.Csv();
	
	OUTPUT @b   
	TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
	USING Outputters.Csv();
	


После отправки запроса на сервер будет отображена схема, демонстрирующая состояние задания.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)


**Запрос данных в озере данных: U-SQL**

После передачи набора данных в озеро данных Azure можно использовать [язык U-SQL](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) для запроса данных и их изучения. Язык U-SQL аналогичен T-SQL, однако при этом он содержит некоторые функции из C#, поэтому пользователи могут писать настраиваемые модули, определяемые пользователями функции и т. д. Можно использовать сценарии из предыдущего шага.

Вскоре после отправки запроса на сервер в **обозревателе озера данных Azure** появится файл tripdata\_summary.CSV. Чтобы просмотреть данные, щелкните файл правой кнопкой мыши.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

Вот как выглядят сведения о файле:

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)


### Кластеры HDInsight Hadoop

**Предварительные требования**

- Создайте учетную запись хранения BLOB-объектов Azure на [портале Azure](http://portal.azure.com). Она используется для хранения данных кластеров HDInsight.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

- Настройте кластеры Hadoop Azure HDInsight на [портале Azure](machine-learning-data-science-customize-hadoop-cluster.md).

	- Во время создания кластера HDInsight с ним необходимо связать созданную учетную запись хранения. Эта учетная запись хранения используется для доступа к данным, которые можно обработать в пределах кластера.
	
![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

	- You must enable **Remote Access** to the head node of the cluster after it is created. Remember the remote access credentials you specify here (different from those specified for the cluster at its creation): you will need them below.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

	- Create an Azure ML workspace. Your Machine Learning Experiments will be stored in this ML workspace.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)


	- Then select the Remote Desktop:

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

	- Upload data using IPython Notebook. First import required packages, plug in credentials, create a db in your storage account, then load data to HDI clusters. 


		#Import required Packages
		import pyodbc
		import time as time
		import json
		import os
		import urllib
		import urllib2
		import warnings
		import re
		import pandas as pd
		import matplotlib.pyplot as plt
		from azure.storage.blob import BlobService
		warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


		#Create the connection to Hive using ODBC
		SERVER_NAME='xxx.azurehdinsight.net'
		DATABASE_NAME='nyctaxidb'
		USERID='xxx'
		PASSWORD='xxxx'
		DB_DRIVER='Microsoft Hive ODBC Driver'
		driver = 'DRIVER={' + DB_DRIVER + '}'
		server = 'Host=' + SERVER_NAME + ';Port=443'
		database = 'Schema=' + DATABASE_NAME
		hiveserv = 'HiveServerType=2'
		auth = 'AuthMech=6'
		uid = 'UID=' + USERID 
		pwd = 'PWD=' + PASSWORD
		CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
		connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
		cursor=connection.cursor()


		#Create Hive database and tables
		queryString = "create database if not exists nyctaxidb;"
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.trip
		                ( 
		                    medallion string, 
		                    hack_license string,
		                    vendor_id string, 
		                    rate_code string, 
		                    store_and_fwd_flag string, 
		                    pickup_datetime string, 
		                    dropoff_datetime string, 
		                    passenger_count int, 
		                    trip_time_in_secs double, 
		                    trip_distance double, 
		                    pickup_longitude double, 
		                    pickup_latitude double, 
		                    dropoff_longitude double, 
		                    dropoff_latitude double)  
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
		
		queryString = """
		                create external table if not exists nyctaxidb.fare 
		                ( 
		                    medallion string, 
		                    hack_license string, 
		                    vendor_id string, 
		                    pickup_datetime string, 
		                    payment_type string, 
		                    fare_amount double, 
		                    surcharge double,
		                    mta_tax double,
		                    tip_amount double,
		                    tolls_amount double,
		                    total_amount double)
		                PARTITIONED BY (month int) 
		                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
		                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
		            """
		cursor.execute(queryString)
	
	
		#Upload data from blob storage to HDI cluster
		for i in range(1,13):
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
		    cursor.execute(queryString)
		    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
		    cursor.execute(queryString)


- Или можно выполнить инструкции по отправке данных о поездках в такси по Нью-Йорку из этого [пошагового руководства](machine-learning-data-science-process-hive-walkthrough.md). Ниже перечислены основные действия.

	- AzCopy: скачивание CSV-файла в формате ZIP из общедоступного BLOB-объекта в локальную папку.
	- AzCopy: отправка распакованного CSV-файла из локальной папки в кластер HDI.
	- Вход в головной узел кластера Hadoop и подготовка к исследовательскому анализу данных.

Данные, загруженные в кластер HDI, можно проверить в обозревателе хранилищ Azure. Кроме того, в кластере HDI находится созданная база данных nyctaxidb.

![](./media/machine-learning-data-science-vm-do-ten-things/Upload_Data_to_HDI_cluster_Azure_Explorer.PNG)


**Просмотр данных: запросы Hive в Python**

Поскольку данные хранятся в кластере Hadoop, можно воспользоваться пакетом pyodbc для подключения к кластерам Hadoop и с помощью Hive выполнить запрос к базе данных на просмотр данных и проектирование характеристик. Вы можете просматривать существующие таблицы, созданные на предыдущем шаге.

	queryString = """
	    show tables in nyctaxidb2;
	    """
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Давайте взглянем в таблице trip на количество записей по каждому месяцу и посмотрим данные по частоте поездок с чаевыми и без.

	queryString = """
	    select month, count(*) from nyctaxidb.trip group by month;
	    """
	results = pd.read_sql(queryString,connection)
	
	%matplotlib inline
	
	results.columns = ['month', 'trip_count']
	df = results.copy()
	df.index = df['month']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)


	queryString = """
	    SELECT tipped, COUNT(*) AS tip_freq 
	    FROM 
	    (
	        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
	        FROM nyctaxidb.fare
	    )tc
	    GROUP BY tipped;
	    """
	results = pd.read_sql(queryString,connection)
	
	results.columns = ['tipped', 'trip_count']
	df = results.copy()
	df.index = df['tipped']
	df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Мы также можем вычислить расстояние между расположениями посадки и высадки, а затем сравнить это значение с дальностью поездки.

	queryString = """
	                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
	                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
	                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance 
	                    from nyctaxidb.trip 
	                    where month=1 
	                        and pickup_longitude between -90 and -30
	                        and pickup_latitude between 30 and 90
	                        and dropoff_longitude between -90 and -30
	                        and dropoff_latitude between 30 and 90;
	            """
	results = pd.read_sql(queryString,connection)
	results.head(5)


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

	results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude', 
	                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
	df = results.loc[results['trip_distance']<=100] #remove outliers
	df = df.loc[df['direct_distance']<=100] #remove outliers
	plt.scatter(df['direct_distance'], df['trip_distance'])


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)


Давайте подготовим сокращенный набор данных (1 %) для моделирования. Эти данные можно использовать в модуле чтения AML.


		queryString = """
	    create  table if not exists nyctaxi_downsampled_dataset_testNEW (
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    pickup_hour string,
	    pickup_week string,
	    weekday string,
	    passenger_count int,
	    trip_time_in_secs double,
	    trip_distance double,
	    pickup_longitude double,
	    pickup_latitude double,
	    dropoff_longitude double,
	    dropoff_latitude double,
	    direct_distance double,
	    payment_type string,
	    fare_amount double,
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double,
	    tipped string,
	    tip_class string
	    )
	    row format delimited fields terminated by ','
	    lines terminated by '\\n'
	    stored as textfile;
		"""
		cursor.execute(queryString)

		--- now insert contents of the join into the above internal table

		queryString = """
	    insert overwrite table nyctaxi_downsampled_dataset_testNEW
	    select
	    t.medallion,
	    t.hack_license,
	    t.vendor_id,
	    t.rate_code,
	    t.store_and_fwd_flag,
	    t.pickup_datetime,
	    t.dropoff_datetime,
	    hour(t.pickup_datetime) as pickup_hour,
	    weekofyear(t.pickup_datetime) as pickup_week,
	    from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
	    t.passenger_count,
	    t.trip_time_in_secs,
	    t.trip_distance,
	    t.pickup_longitude,
	    t.pickup_latitude,
	    t.dropoff_longitude,
	    t.dropoff_latitude,
	    t.direct_distance,
	    f.payment_type,
	    f.fare_amount,
	    f.surcharge,
	    f.mta_tax,
	    f.tip_amount,
	    f.tolls_amount,
	    f.total_amount,
	    if(tip_amount>0,1,0) as tipped,
	    if(tip_amount=0,0,
	    if(tip_amount>0 and tip_amount<=5,1,
	    if(tip_amount>5 and tip_amount<=10,2,
	    if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
	    from
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    pickup_datetime,
	    dropoff_datetime,
	    passenger_count,
	    trip_time_in_secs,
	    trip_distance,
	    pickup_longitude,
	    pickup_latitude,
	    dropoff_longitude,
	    dropoff_latitude,
	    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
	    radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
	    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
	    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
	    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
	    rand() as sample_key
	
	    from trip
	    where pickup_latitude between 30 and 90
	        and pickup_longitude between -90 and -30
	        and dropoff_latitude between 30 and 90
	        and dropoff_longitude between -90 and -30
	    )t
	    join
	    (
	    select
	    medallion,
	    hack_license,
	    vendor_id,
	    pickup_datetime,
	    payment_type,
	    fare_amount,
	    surcharge,
	    mta_tax,
	    tip_amount,
	    tolls_amount,
	    total_amount
	    from fare
	    )f
	    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
	    where t.sample_key<=0.01
		"""
		cursor.execute(queryString)

Через некоторое время вы увидите, что данные были загружены в кластеры Hadoop.
		
	queryString = """
	    select * from nyctaxi_downsampled_dataset limit 10;
	    """
	cursor.execute(queryString)
	pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)


**Чтение данных из HDI с использованием AML: модуль чтения AML**

Чтобы получить доступ к базе данных в кластере Hadoop, в студии машинного обучения можно также использовать **модуль чтения**. Укажите учетные данные кластеров HDI и учетной записи хранения Azure, и вы сможете создавать модели машинного обучения с помощью базы данных в кластерах HDI.

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

Затем можно просмотреть оцененный набор данных.

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)


### Хранилище данных SQL Azure и базы данных

#### Azure DocumentDB.

Azure DocumentDB — это база данных NoSQL в облаке. Она позволяет работать с документами, например JSON, хранить их и выполнять к ним запросы.

Чтобы получить доступ к DocumentDB из DSVM, необходимо выполнить приведенные ниже предварительные действия.

1. Установить пакет SDK для DocumentDB Python (запустить ```pip install pydocumentdb``` из командной строки).
1. Создать учетную запись DocumentDB и базу данных DocumentDB на [портале Azure](https://portal.azure.com).
1. Скачать средство миграции DocumentDB [здесь](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) и извлечь нужный каталог.
1. Импортировать данные JSON (данные о вулканах), хранящиеся в [общедоступном BLOB-объекте](https://cahandson.blob.core.windows.net/samples/volcano.json), в DocumentDB с помощью следующих параметров команды в средство миграции (dtui.exe из каталога, где установлено средство миграции DocumentDB). Ввести указанные ниже параметры исходного и целевого расположений. 

	/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

После импорта данных можно перейти в Jupyter и открыть записную книжку с названием ```DocumentDBSample```, содержащую код Python для доступа к DocumentDB и выполнения базовых запросов. Дополнительные сведения о DocumentDB см. на [странице документации](https://azure.microsoft.com/documentation/learning-paths/documentdb/) службы.


## 8\. Создание отчетов и панели мониторинга с помощью Power BI Desktop 

Давайте визуализируем в службе Power BI JSON-файл с данными о вулканах, который мы видели в приведенном выше примере о DocumentDB, чтобы провести визуальный анализ данных. Подробные инструкции см. в [статье о Power BI](../documentdb/documentdb-powerbi-visualize.md). Далее приведены основные шаги.

1. Откройте Power BI Desktop и щелкните "Получение данных". Укажите URL-адрес: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Записи JSON будут импортированы в виде списка.
3. Преобразуйте список в таблицу, чтобы служба PowerBI могла работать с теми же данными.
4. Расширьте столбцы, щелкнув значок развертывания (значок с изображением стрелки влево и стрелки вправо, который находится справа от столбца).
5. Вы увидите, что расположение является полем "Запись". Разверните запись и выберите только координаты. Координаты указаны в столбце списка.
6. Затем добавьте новый столбец для преобразования столбца координат в разделенный запятыми столбец LatLong, содержащий два элемента в поле списка координат, используя формулу ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```. 
7. И, наконец, преобразуйте столбец ```Elevation``` в столбец с десятичными значениями и нажмите кнопку "Закрыть и применить".

Вместо выполнения описанных выше шагов можно вставить приведенный ниже код, который создает сценарий для шагов выше в расширенном редакторе в PowerBI, позволяющем писать преобразования данных на языке запросов.


	let
	    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
	    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
	    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
	    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
	    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
	    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
	in
	    #"Changed Type"
		


Теперь ваши данные находятся в модели данных Power BI. Power BI Desktop должен выглядеть, как показано ниже.

![](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

Вы можете приступить к созданию отчетов и визуализации с помощью модели данных. Инструкции по созданию отчетов см. в этой [статье о Power BI](../documentdb/documentdb-powerbi-visualize.md#build-the-reports). Конечным результатом будет отчет, который выглядит следующим образом:


Подлежит уточнению: отсутствует или указан неправильный URL-адрес изображения отчета с картой вулканов.

## 9\. Динамическое масштабирование DSVM в соответствии с требованиями проекта

По мере необходимости можно увеличивать или уменьшать масштаб DSVM. Если виртуальную машину не требуется использовать вечерами или в выходные дни, вы можете просто завершить ее работу на [портале Azure](https://portal.azure.com). ПРИМЕЧАНИЕ. Если вы используете только кнопку завершения работы операционной системы на виртуальной машине, с вас будет взиматься плата за вычислительные ресурсы.

Если вам необходимо выполнять анализ в крупных масштабах и требуются дополнительные ресурсы ЦП, памяти и диска, к вашим услугам предлагается большой выбор размеров виртуальных машин с точки зрения ядер ЦП, объема памяти и типов дисков (включая твердотельные накопители), соответствующих вычислительным потребностям и финансовым возможностям. Полный список виртуальных машин, а также их почасовой тариф см. на странице [Цены на виртуальные машины Azure](https://azure.microsoft.com/pricing/details/virtual-machines/).

Аналогично, если ваши потребности в вычислительной мощности виртуальной машины сократились (например, вы переместили основную рабочую нагрузку в кластер Hadoop или Spark), вы можете уменьшить масштаб кластера на [портале Azure](https://portal.azure.com) и перейти к настройкам параметров экземпляра виртуальной машины. Ниже приведен снимок экрана.


![](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)


## 10\. Установка дополнительных инструментов на виртуальной машине

Мы упаковали несколько средств, которые, по нашему мнению, смогут удовлетворить множество различных потребностей аналитики данных и сэкономить время благодаря отсутствию необходимости установки и последующей настройки среды и оплате только используемых ресурсов. Как говорилось ранее в этой статье, вы можете использовать другие службы данных и службы аналитики Azure, позволяющие усовершенствовать имеющуюся среду аналитики. Мы понимаем, что в некоторых случаях могут потребоваться дополнительные инструменты, включая некоторые инструменты сторонних производителей. На этой виртуальной машине вы располагаете полным административным доступом, позволяющим устанавливать новые необходимые вам средства. Вы также можете установить дополнительные пакеты на Python и R, которые не предустановлены. Для Python можно использовать ```conda``` или ```pip```. Для R можно использовать ```install.packages()``` в консоли R либо в IDE выбрать "Пакеты"-> "Установить пакеты".

Это всего лишь несколько задач, которые можно выполнить в виртуальной машине Майкрософт для обработки и анализа данных. На самом деле существует множество вещей, позволяющих сделать ее эффективной аналитической средой.

<!---HONumber=AcomDC_0218_2016-->