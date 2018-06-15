---
title: 10 задач, которые можно выполнить в виртуальной машине для обработки и анализа данных в Azure | Документация Майкрософт
description: Выполните различные задачи по изучению и моделированию данных в виртуальной машине для анализа и обработки данных.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: gokuma
ms.openlocfilehash: a5f0961a99eac805e82cbc5e5d61c485f8661ba0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31595758"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>10 задач, которые можно выполнить на виртуальной машине Windows для обработки и анализа данных

Виртуальная машина Windows для обработки и анализа данных Майкрософт (DSVM) — это мощная среда разработки научных данных, позволяющая выполнять различные задачи по исследованию и моделированию данных. Среда поставляется в уже готовом виде и входит в пакет нескольких распространенных средств анализа данных, которые помогают легко и быстро начать анализ для локального, облачного или гибридного развертывания. DSVM тесно взаимодействует со многими службами Azure и может читать и обрабатывать данные, уже хранящиеся в Azure в хранилище данных SQL Azure, Azure Data Lake, хранилище Azure или в Azure Cosmos DB. Она также может использовать другие инструменты аналитики, такие как машинное обучение Azure и фабрика данных Azure.

В этой статье содержатся сведения об использовании DSVM для выполнения различных задач обработки и анализа данных, а также взаимодействия с другими службами Azure. Вот некоторые задачи, которые можно выполнить в DSVM:

1. Просмотр данных и локальная разработка моделей на DSVM с помощью Microsoft ML Server, Python.
2. Использование записной книжки Jupyter для проведения экспериментов с данными в браузере с помощью Python 2, Python 3, Microsoft R (готовой корпоративной версии R для обеспечения улучшенной производительности).
3. Развертывание моделей, созданных с помощью R и Python, в эксплуатацию в службе машинного обучения Azure, чтобы клиентские приложения могли обращаться к ним с использованием простого интерфейса веб-службы.
4. Администрирование ресурсов Azure с помощью портала Azure или PowerShell.
5. Увеличение объема хранилища и предоставление всем членам команды общего доступа к большим наборам данных и коду за счет создания файлового хранилища Azure в виде подключаемого диска в DSVM.
6. Совместное использование кода командой с помощью GitHub и доступ к репозиторию с помощью предварительно установленных клиентов Git — Git Bash, Git GUI.
7. Доступ к различным службам данных и службам аналитики Azure, таким как хранилище BLOB-объектов, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, хранилище данных SQL Azure и базы данных Azure.
8. Создание отчетов и панелей мониторинга с помощью службы Power BI Desktop, предустановленной на DSVM, и их развертывание в облаке
9. Динамическое масштабирование DSVM в соответствии с требованиями проекта
10. Установка дополнительных инструментов на виртуальной машине   

> [!NOTE]
> За использование многих дополнительных хранилищ данных и аналитических служб, перечисленных в этой статье, взимается дополнительная плата. Подробные сведения см. на странице [Цены Azure](https://azure.microsoft.com/pricing/).
> 
> 

**Предварительные требования**

* Вам понадобится подписка Azure. Вы можете зарегистрироваться [здесь](https://azure.microsoft.com/free/), чтобы получить бесплатную пробную версию.
* Инструкции по подготовке виртуальной машины для обработки и анализа данных на портале Azure доступны в статье [Создание виртуальной машины](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Просмотр данных и разработка моделей с помощью Microsoft ML Server или Python
Используя такие языки, как R и Python, анализ данных можно выполнять непосредственно в DSVM.

При работе с R можно использовать IDE, такую как RStudio, которая находится в меню "Пуск" или на рабочем столе. Кроме того, вы можете воспользоваться инструментами R для Visual Studio. Корпорация Майкрософт предоставила дополнительные библиотеки поверх R с открытым исходным кодом или CRAN-R для осуществления масштабируемой аналитики и анализа данных, размер которых превышает разрешенный объем памяти, путем выполнения параллельной и фрагментарной обработки данных. 

Для Python можно использовать такую интегрированную среду разработки, как Visual Studio Community Edition, в которой предустановлено расширение Python Tools for Visual Studio (PTVS). По умолчанию в PTVS настроена только корневая среда Anaconda Python 3.6. Чтобы включить Anaconda Python 2.7, необходимо выполнить следующие действия.

* Создайте настраиваемые среды для каждой версии, последовательно выбрав **Инструменты** -> **Python Tools** (Инструменты Python) -> **Python Environments** (Среды Python), а затем щелкнув **+ Custom** (+ Настраиваемые) в Visual Studio 2015 Community Edition.
* Введите описание и задайте путь префиксов сред для Anaconda Python 2.7 — *c:\anaconda\envs\python2*.
* Щелкните **Автообнаружение**, а затем — **Применить**, чтобы сохранить среду.

Вот как выглядит установка настраиваемой среды в Visual Studio.

![Установка PTVS](./media/vm-do-ten-things/PTVSSetup.png)

Дополнительные сведения о создании сред Python см. в [документации по PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it).

Теперь вы можете создать новый проект Python. Щелкните **Файл** -> **Создать** -> **Проект** -> **Python** и выберите тип создаваемого приложения Python. Вы можете указать нужную версию среды Python для текущего проекта (Anaconda 2.7 или 3.6). Для этого щелкните правой кнопкой мыши **Python environment** (Среда Python), выберите **Add/Remove Python Environments** (Добавление или удаление сред Python), а затем выберите требуемую среду. Дополнительные сведения о работе с PTVS можно найти на странице [документации](https://github.com/Microsoft/PTVS/wiki) по продукту.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Использование записной книжки Jupyter для изучения и моделирования данных с помощью Python или R
Записная книжка Jupyter представляет собой мощную среду IDE с поддержкой браузера для изучения и моделирования данных. В записной книжке Jupyter можно использовать Python 2, Python 3 или R (с открытым исходным кодом и Microsoft R Server).

Чтобы запустить записную книжку Jupyter, щелкните значок меню "Пуск" или значок на рабочем столе с именем **Записная книжка Jupyter**. В командной строке DSVM можно выполнить команду ```jupyter notebook``` из каталога, в котором находятся записные книжки или для которого вы хотите их создать.  

В записной книжке Jupyter вы найдете каталог, содержащий несколько примеров записных книжек, которые предварительно упакованы в DSVM. Теперь вы можете:

* щелкнуть записную книжку, чтобы просмотреть код;
* выполнить отдельную ячейку, нажав клавиши **SHIFT+ВВОД**;
* выполнить всю записную книжку, последовательно щелкнув **Ячейка** -> **Выполнить**;
* создать записную книжку, щелкнув значок Jupyter (в левом верхнем углу), нажать кнопку **Создать** справа и выбрать язык записной книжки (также известный как ядра).   

> [!NOTE]
> В настоящее время Jupyter поддерживает ядра Python 2.7, Python 3.6, R, Julia и PySpark. Ядро R поддерживает программирование на R с открытым исходным кодом и на производительной платформе Microsoft R.   
> 
> 

Выполнив вход в записную книжку, вы можете изучать данные, создавать модели и тестировать их с использованием нужных библиотек.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Создание моделей с помощью R или Python и их ввод в эксплуатацию с помощью машинного обучения Azure
После создания и проверки модели обычно следует этап развертывания этой модели в рабочей среде. Это позволит клиентским приложениям вызывать прогнозы модели в режиме реального времени или на основе пакетного режима. Службе машинного обучения Azure доступен механизм ввода в эксплуатацию модели, созданной на языке R или Python.

После ввода модели в эксплуатацию в службе машинного обучения Azure становится доступна веб-служба, позволяющая клиентам выполнять вызовы REST, которые передают входные параметры и получают прогнозы из модели в виде выходных данных.   

> [!NOTE]
> Если вы еще не зарегистрировались для использования службы машинного обучения Azure, вы можете получить бесплатную или стандартную рабочую область. Для этого на странице [Microsoft Azure Machine Learning Studio](https://studio.azureml.net/) (Студия машинного обучения Azure) щелкните Getting Started (Приступая к работе).   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Создание и ввод в эксплуатацию моделей Python
Ниже приведен фрагмент кода, разработанный в записной книжке Jupyter Python, который создает простую модель с помощью библиотеки SciKit-learn.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

Метод, используемый для развертывания моделей Python в службе машинного обучения Azure, обертывает прогноз модели в функцию и добавляет в нее атрибуты, которые предоставляются предустановленной библиотекой Python службы машинного обучения Azure и обозначают ИД рабочей области, ключ API, а также входные и возвращаемые параметры службы машинного обучения.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Теперь клиент может выполнять вызовы веб-службы. Существуют удобные оболочки для создания запросов REST API. Ниже приведен пример кода использования веб-службы.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Библиотека машинного обучения Azure в настоящее время поддерживается только в Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Создание и ввод в эксплуатацию моделей R
Процедура развертывания моделей R, построенных в виртуальной машине DSVM или в другом компоненте службы машинного обучения Azure, аналогична действиям, выполняемым для Python. Для этого выполните следующие действия:

* Создайте файл settings.json, чтобы указать идентификатор рабочей области и маркер проверки подлинности. 
* Напишите оболочку для функции прогнозирования модели.
* Вызовите ```publishWebService``` в библиотеке машинного обучения Azure для передачи оболочки функции.  

Ниже приведена процедура и фрагменты кода, которые могут использоваться для настройки, создания, публикации и использования модели как веб-службы в Машинном обучении Azure.

#### <a name="setup"></a>Настройка

* Создайте файл Settings.json в каталоге с именем ```.azureml```, который находится в корневом каталоге, и введите параметры из рабочей области машинного обучения Azure.

структура файла Settings.json:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Создание модели на языке R и ее публикация в службе машинного обучения Azure
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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Использование модели, развернутой в службе машинного обучения Azure
Для использования модели из клиентского приложения мы используем библиотеку службы машинного обучения Azure, чтобы найти опубликованную веб-службу по имени с помощью вызова API `services` для определения конечной точки. Затем нужно просто вызвать функцию `consume` и передать блок данных для прогноза.
Приведенный ниже код позволяет использовать модель, опубликованную как веб-служба машинного обучения Azure.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Дополнительные сведения о библиотеке R машинного обучения Azure можно найти [здесь](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Администрирование ресурсов Azure с помощью портала Azure или PowerShell.
DSVM позволяет не только создавать аналитическое решение локально на виртуальной машине, но и предоставляет доступ к службам в облаке Azure корпорации Майкрософт. Azure предоставляет несколько вычислительных служб, служб хранения данных и служб аналитики данных, а также ряд других служб, которые можно администрировать и к которым можно обращаться с DSVM.

Для администрирования подписки и облачных ресурсов Azure можно воспользоваться браузером и перейти на [портал Azure](https://portal.azure.com). Кроме того, Azure PowerShell позволяет осуществлять администрирование подписки и ресурсов Azure с помощью сценариев.
Вы можете запустить Azure PowerShell с помощью ярлыка на рабочем столе или выбрать в меню "Пуск" компонент Microsoft Azure PowerShell. Дополнительные сведения об администрировании подписки и ресурсов Azure с помощью сценариев Windows PowerShell см. в [документации по Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md).

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Увеличение объема хранилища за счет общей файловой системы
Специалисты по обработке и анализу данных могут использовать большие наборы данных, код или другие ресурсы вместе с участниками группы. В самой виртуальной машине DSVM доступно около 45 ГБ свободного места. Чтобы увеличить объем хранилища, можно использовать службу файлов Azure и либо подключить к одному или нескольким экземплярам DSVM, либо получить к ней доступ через REST API.  Кроме того, для добавления дополнительных выделенных дисков данных можно использовать [портал Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) или [Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md). 

> [!NOTE]
> Максимальный объем общей папки службы файлов Azure равен 5 ТБ, а максимальный размер отдельного файла составляет 1 ТБ. 
> 
> 

Общую папку службы файлов Azure можно создать с помощью Azure PowerShell. Далее приведен сценарий, выполняемый в Azure PowerShell для создания общего ресурса службы файлов Azure.

    # Authenticate to Azure.
    Connect-AzureRmAccount
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

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Теперь к этому диску можно обращаться как к обычному диску на виртуальной машине.

## <a name="6-share-code-with-your-team-using-github"></a>6. Совместное использование кода командой с помощью GitHub
GitHub — это репозиторий кода, в котором находится множество примеров кода и исходного кода для различных инструментов на основе разных технологий, совместно используемых сообществом разработчиков. Git выступает в качестве технологии для отслеживания и хранения версий файлов кода. GitHub также является платформой, позволяющей создавать пользовательские репозитории для хранения общего кода и документации команды, реализовать систему управления версиями и определить, какие пользователи получат доступ к просмотру и разработке кода. Дополнительные сведения об использовании Git см. на [страницах справки GitHub](https://help.github.com/). GitHub можно использовать как вариант совместной работы команды. Можно использовать разработанный сообществом код и участвовать в разработке кода сообществом.

DSVM уже поставляется с набором клиентских инструментов, поддерживающих доступ к репозиторию GitHub как через командную строку, так и через графический пользовательский интерфейс. Программа командной строки для работы с Git и GitHub называется Git Bash. Среда Visual Studio, установленная на виртуальной машине DSVM, имеет расширения Git. Значки для запуска этих средств находятся в меню "Пуск" и на рабочем столе.

Для скачивания кода из репозитория GitHub используйте команду ```git clone```. Например, чтобы скачать репозиторий обработки и анализа данных, опубликованный корпорацией Майкрософт в текущем каталоге, выполните указанную ниже команду после входа в ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

В Visual Studio можно выполнить ту же операцию клонирования. На следующем снимке экрана показано, как получить доступ к средствам Git и GitHub в Visual Studio.

![Git в Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Дополнительные сведения об использовании Git для работы с репозиторием GitHub доступны в ряде ресурсов на сайте github.com. Вы также найдете полезную информацию в этой [памятке](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) .

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Доступ к различным службам данных и службам аналитики Azure
### <a name="azure-blob"></a>Большой двоичный объект Azure
Большой двоичный объект Azure является надежным и экономичным облачным хранилищем для больших и малых объемов данных. В этом разделе содержатся сведения о том, как можно переместить данные в большой двоичный объект Azure и получить доступ к хранящимся в нем данным.

**Предварительные требования**

* **Создайте учетную запись хранения BLOB-объектов Azure на [портале Azure](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Убедитесь, что предустановленное средство командной строки AzCopy находится по адресу ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Каталог, содержащий файл Azcopy.exe, уже находится в переменной среды PATH. Поэтому вы можете не вводить полный путь команды при запуске этого средства. Дополнительные сведения об инструменте AzCopy см. в статье [Перенос данных с помощью AzCopy для Windows](../../storage/common/storage-use-azcopy.md).
* Запустите инструмент Azure Storage Explorer. Его можно скачать с сайта [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Перемещение данных из виртуальной машины в BLOB-объект Azure: AzCopy**

Для перемещения данных между локальными файлами и хранилищем BLOB-объектов можно использовать AzCopy в командной строке или PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Замените **C:\myfolder** путем к расположению, где хранится файл, **mystorageaccount** — именем учетной записи хранилища BLOB-объектов, **mycontainer** — именем контейнера, а значение в поле **Ключ учетной записи хранения** замените ключом доступа к хранилищу BLOB-объектов. Данные вашей учетной записи хранения находятся на [портале Azure](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Выполните команду AzCopy в PowerShell или из командной строки. Далее приведен пример использования команды AzCopy.

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Вскоре после выполнения команды AzCopy для копирования в большой двоичный объект Azure файл появится в обозревателе службы хранилища Azure.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Перемещение данных из виртуальной машины в BLOB-объект Azure: обозреватель хранилищ Azure**

Отправить данные из локального файла в виртуальной машине можно также с помощью обозревателя хранилищ Azure.

* Чтобы передать данные в контейнер, выберите целевой контейнер и нажмите кнопку **Передать**.![Передача данных в обозревателе хранилищ](./media/vm-do-ten-things/storage-accounts.png)
* Щелкните знак многоточия **…** справа от поля **Файлы**, выберите в файловой системе один или несколько файлов для передачи и нажмите кнопку **Передать**, чтобы начать их передачу.![Передача файлов в большой двоичный объект](./media/vm-do-ten-things/upload-files-to-blob.png)

**Чтение данных из большого двоичного объекта Azure: модуль "Читатель" машинного обучения**

Для чтения данных из большого двоичного объекта в Студии машинного обучения Azure можно использовать модуль **Импорт данных**.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Чтение данных из BLOB-объекта Azure: Python ODBC**

Для чтения данных непосредственно из большого двоичного объекта в записной книжке Jupyter или программе Python можно использовать библиотеку **BlobService** .

Сначала импортируйте необходимые пакеты.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
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

    #download from blob
    t1 = time.time()
    blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
    t2 = time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

Данные считываются в виде блока данных, как показано ниже.

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake;
Хранилище Azure Data Lake является гипермасштабируемым репозиторием для рабочих нагрузок аналитической обработки больших данных, совместимым с распределенной файловой системой Hadoop (HDFS). Оно работает с Hadoop, Spark и Azure Data Lake Analytics. В этом разделе вы узнаете, как переместить данные в Azure Data Lake Store и запустить анализ с помощью Azure Data Lake Analytics.

**Предварительные требования**

* Создайте экземпляр Azure Data Lake Analytics на [портале Azure](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Инструменты **Azure Data Lake Tools** для **Visual Studio**, доступные по этой [ссылке](https://www.microsoft.com/download/details.aspx?id=49504), уже установлены в выпуске Visual Studio Community Edition на виртуальной машине. После запуска Visual Studio и входа в подписку Azure вы увидите свою учетную запись анализа данных Azure и хранилище в левой области Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Перемещение данных из виртуальной машины в озеро данных: обозреватель озера данных Azure**

**Azure Data Lake Explorer** можно использовать для передачи данных из локальных файлов в виртуальной машине в хранилище Data Lake.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Можно также создать конвейер данных для перемещения данных в Azure Data Lake или из него с помощью [фабрики данных Azure (ADF)](https://azure.microsoft.com/services/data-factory/). В этой [записи блога](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) содержатся сведения об этапах, необходимых для создания конвейеров данных.

**Чтение данных из BLOB-объекта Azure в озеро данных: U-SQL**

Если данные хранятся в хранилище BLOB-объектов Azure, их можно считывать непосредственно из BLOB-объекта хранилища Azure в запросе U-SQL. Перед построением запроса U-SQL убедитесь, что учетная запись хранения BLOB-объектов связана с озером данных Azure. На **портале Azure** найдите панель мониторинга для Azure Data Lake Analytics, щелкните **Добавить источник данных**, в качестве типа хранилища выберите **Служба хранилища Azure** и укажите имя и ключ учетной записи хранения Azure. Затем вы сможете ссылаться на данные, хранящиеся в учетной записи хранения.

![Ввод имени учетной записи хранения и ключа](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

В Visual Studio можно читать данные из хранилища BLOB-объектов, выполнять обработку данных, проектировать характеристики и выводить полученные данные в озеро данных Azure или хранилище BLOB-объектов Azure. Ссылаясь на данные в хранилище BLOB-объектов, используйте **wasb://**, а на данные в Azure Data Lake — **swbhdfs://**.

![Кадр данных](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

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



После отправки запроса на сервер отображается схема, демонстрирующая состояние задания.

![Схема состояния задания](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Запрос данных в озере данных: U-SQL**

После передачи набора данных в Azure Data Lake можно использовать [язык U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md), чтобы запрашивать данные и затем изучать их. Язык U-SQL аналогичен T-SQL, но содержит некоторые функции из C#, поэтому пользователи могут писать настраиваемые модули, определяемые пользователями функции и т. д. Можно использовать сценарии из предыдущего шага.

Вскоре после отправки запроса на сервер в **Azure Data Lake Explorer** появится файл tripdata_summary.CSV. Чтобы просмотреть данные, щелкните этот файл правой кнопкой мыши.

![Файл в Azure Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Вот как выглядят сведения о файле:

![Сведения о файле](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Кластеры HDInsight Hadoop
Azure HDInsight является управляемой службой Apache Hadoop, Spark, HBase и Storm в облаке. Вы можете легко работать с кластерами Azure HDInsight на виртуальной машине для обработки и анализа данных.

**Предварительные требования**

* Создайте учетную запись хранения BLOB-объектов Azure на [портале Azure](https://portal.azure.com). Она используется для хранения данных кластеров HDInsight.

![Создание учетной записи хранения больших двоичных объектов Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Настройте кластеры HDInsight Hadoop в Azure на [портале Azure](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Во время создания кластера HDInsight свяжите с ним созданную учетную запись хранения. Эта учетная запись хранения используется для доступа к данным, которые можно обработать в пределах кластера.

![Связывание созданной учетной записи хранения с кластером HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* После создания кластера включите **удаленный доступ** к головному узлу кластера. Запомните указываемые здесь учетные данные для удаленного доступа, так как они потребуются при выполнении следующей процедуры.

![Включение удаленного доступа](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Создайте рабочую область машинного обучения Azure. В ней будут храниться эксперименты машинного обучения. Выберите выделенные параметры на портале, как показано на следующем снимке экрана:

![Создание рабочей области машинного обучения Azure](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Затем введите параметры для рабочей области машинного обучения.

![Ввод параметров рабочей области машинного обучения](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Загрузите данные с помощью IPython Notebook. Сначала импортируйте необходимые пакеты, подключите учетные данные, создайте базу данных в своей учетной записи хранения, а затем загрузите данные в кластеры HDI.

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


* Или выполните инструкции по передаче данных о поездках в такси по Нью-Йорку из этого [пошагового руководства](../team-data-science-process/hive-walkthrough.md). Ниже перечислены основные действия.
  
  * AzCopy: скачивание CSV-файла в формате ZIP из общедоступного BLOB-объекта в локальную папку.
  * AzCopy: отправка распакованного CSV-файла из локальной папки в кластер HDI.
  * Вход в головной узел кластера Hadoop и подготовка к исследовательскому анализу данных.

Данные, загруженные в кластер HDI, можно проверить в обозревателе хранилищ Azure. Кроме того, в кластере HDI находится созданная база данных nyctaxidb.

**Просмотр данных: запросы Hive в Python**

Поскольку данные хранятся в кластере Hadoop, можно воспользоваться пакетом pyodbc для подключения к кластерам Hadoop и с помощью Hive выполнить запрос к базе данных на просмотр данных и проектирование характеристик. Вы можете просматривать существующие таблицы, созданные на предыдущем шаге.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Просмотр имеющихся таблиц](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

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


![Диаграмма количества записей за каждый месяц](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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


![Диаграмма частотности чаевых](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Вы также можете вычислить расстояние между расположениями посадки и высадки, а затем сравнить это значение с дальностью поездки.

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


![Таблица данных о посадке и высадке](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Диаграмма сравнения расстояний между расположениями посадки и высадки с дальностью поездки](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Подготовим сокращенный набор данных (1 %) для моделирования. Эти данные можно использовать в модуле "Читатель" машинного обучения.

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

        --- now insert contents of the join into the preceding internal table

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


![Таблицы данных](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Чтение данных из HDI с помощью машинного обучения: модуль "Читатель"**

Чтобы получить доступ к базе данных в кластере Hadoop, в Студии машинного обучения можно также использовать **модуль "Читатель"**. Укажите учетные данные кластеров HDI и учетной записи хранения Azure, чтобы разрешить создание моделей машинного обучения с помощью базы данных в кластерах HDI.

![Свойства модуля "Читатель"](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Затем можно просмотреть оцененный набор данных.

![Просмотр оцененного набора данных](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Хранилище данных SQL Azure и базы данных
Хранилище данных SQL Azure — это хранилище эластичных данных "как услуга" с возможностями SQL Server корпоративного класса.

Хранилище данных SQL Azure можно подготовить, следуя инструкциям, приведенным в этой [статье](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). После подготовки хранилища данных SQL Azure вы можете использовать это [пошаговое руководство](../team-data-science-process/sqldw-walkthrough.md) для передачи, просмотра и моделирования данных из хранилища данных SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB — это база данных NoSQL в облаке. Она позволяет работать с такими документами, как JSON, хранить их и выполнять к ним запросы.

Чтобы получить доступ к Azure Cosmos DB из DSVM, необходимо выполнить приведенные ниже предварительные действия.

1. Пакет SDK Python для Azure Cosmos DB уже установлен на DSVM (для обновления выполните команду ```pip install pydocumentdb --upgrade``` из командной строки)
2. Создайте учетную запись и базу данных Azure Cosmos DB на [портале Azure](https://portal.azure.com).
3. Скачайте средство миграции Azure Cosmos DB [отсюда](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) и извлеките нужный вам каталог.
4. Импортируйте данные JSON (данные о вулканах), хранящиеся в [общедоступном большом двоичном объекте](https://cahandson.blob.core.windows.net/samples/volcano.json), в Cosmos DB с помощью следующих параметров команды средства миграции (файл dtui.exe из каталога, в который вы установили средство миграции Cosmos DB). Введите исходное и целевое расположение со следующими параметрами:
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

После импорта данных перейдите в Jupyter и откройте записную книжку *DocumentDBSample*, содержащую код Python для доступа к Azure Cosmos DB и выполнения базовых запросов. Дополнительные сведения о Cosmos DB см. на [странице документации](https://docs.microsoft.com/azure/cosmos-db/) службы.

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Создание отчетов и панели мониторинга с помощью Power BI Desktop
В службе Power BI можно визуализировать JSON-файл с данными о вулканах из предыдущего примера с Cosmos DB, чтобы выполнить визуальный анализ данных. Подробные указания см. в статье о [Power BI](../../cosmos-db/powerbi-visualize.md). Ниже приведены основные действия.

1. Откройте Power BI Desktop и щелкните Get Data (Получение данных). Укажите URL-адрес, например https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Вы увидите, что записи JSON будут импортированы в виде списка.
3. Преобразуйте список в таблицу, чтобы служба Power BI могла работать с теми же данными.
4. Затем расширьте столбцы, щелкнув значок развертывания (значок с изображением стрелки влево и стрелки вправо, который находится справа от столбца).
5. Обратите внимание, что расположение является полем "Запись". Разверните запись и выберите только координаты. Координаты указаны в столбце списка.
6. Добавьте новый столбец для преобразования столбца координат в разделенный запятыми столбец LatLong, содержащий два сцепленных элемента в поле списка координат, используя формулу ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. И наконец, преобразуйте столбец ```Elevation``` в столбец с десятичными значениями и щелкните **Закрыть** и **Применить**.

Вместо выполнения описанных выше шагов можно вставить следующий код. Он создает скрипт на основе действий, примененных в расширенном редакторе Power BI, позволяя записывать преобразования данных на языке запросов.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Теперь ваши данные находятся в модели данных Power BI. Рабочий стол Power BI Desktop должен выглядеть так:

![Power BI Desktop;](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Вы можете приступить к созданию отчетов и визуализации с помощью модели данных. Указания по созданию отчетов см. в этой [статье о Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports). Выходные данные — это отчет, который выглядит следующим образом.

![Представление отчета в Power BI Desktop — соединитель Power BI](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Динамическое масштабирование DSVM в соответствии с требованиями проекта
По мере необходимости можно увеличивать или уменьшать масштаб DSVM. Если виртуальную машину не требуется использовать вечерами или в выходные дни, то вы можете просто завершить ее работу на [портале Azure](https://portal.azure.com).

> [!NOTE]
> Если вы используете только кнопку завершения работы операционной системы на виртуальной машине, с вас будет взиматься плата за вычислительные ресурсы.  
> 
> 

Если необходимо выполнить масштабный анализ, и вам нужны дополнительные ресурсы ЦП, памяти и (или) диска, вы можете выбрать требуемый размер виртуальных машин с учетом таким характеристик, как число ядер ЦП, наличие графического процессора для глубокого обучения, объем памяти и типы дисков (включая твердотельные накопители), соответствующих вашим вычислительным потребностям и финансовым возможностям. Полный список виртуальных машин, а также их почасовой тариф см. на странице [Цены на виртуальные машины Linux](https://azure.microsoft.com/pricing/details/virtual-machines/).

Аналогично, если ваши потребности в вычислительной мощности виртуальной машины сократились (например, вы переместили основную рабочую нагрузку в кластер Hadoop или Spark), то вы можете уменьшить масштаб кластера на [портале Azure](https://portal.azure.com) и перейти к настройкам параметров экземпляра виртуальной машины. Ниже приведен снимок экрана.

![Параметры экземпляра виртуальной машины](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Установка дополнительных инструментов на виртуальной машине
Многие задачи по анализу данных можно выполнить с помощью предустановленных в DSVM средств. Они помогут вам экономить такие ресурсы, как время (нет необходимости в установке и последующей настройке среды) и деньги (выставление счетов только за используемые ресурсы).

Вы можете использовать другие службы данных и службы аналитики Azure, упомянутые в этой статье, чтобы усовершенствовать существующую среду аналитики. В определенных случаях могут потребоваться дополнительные средства, включая некоторые инструменты сторонних производителей. На этой виртуальной машине вы располагаете полным административным доступом, позволяющим устанавливать новые необходимые вам средства. Вы также можете установить дополнительные пакеты на Python и R, которые не предустановлены. Для Python можно использовать ```conda``` или ```pip```. Для R можно использовать ```install.packages()``` в консоли R либо выбрать **Пакеты** -> **Install Packages** (Установить пакеты) в интегрированной среде разработки.

## <a name="summary"></a>Сводка
Это только часть задач, которые можно выполнить на виртуальной машине Майкрософт для обработки и анализа данных. На самом деле существует множество вещей, позволяющих сделать ее эффективной аналитической средой.

