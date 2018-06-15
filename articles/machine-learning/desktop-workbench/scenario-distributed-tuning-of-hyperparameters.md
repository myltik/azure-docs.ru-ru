---
title: Распределенная настройка гиперпараметров с помощью Azure Machine Learning Workbench | Документация Майкрософт
description: В этом сценарии показана распределенная настройка гиперпараметров с помощью Azure Machine Learning Workbench
services: machine-learning
author: pechyony
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.author: dmpechyo
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.date: 09/20/2017
ms.openlocfilehash: c6eccda4329572a181b6a7e7e3870ace4bfac13b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832753"
---
# <a name="distributed-tuning-of-hyperparameters-using-azure-machine-learning-workbench"></a>Распределенная настройка гиперпараметров с помощью Azure Machine Learning Workbench

В этом сценарии показано, как с помощью Azure Machine Learning Workbench масштабировать настройку гиперпараметров алгоритмов машинного обучения, реализующих API scikit-learn. Мы покажем, как настроить и использовать удаленный контейнер Docker и кластер Spark в качестве серверной части для выполнения настройки гиперпараметров.

## <a name="link-of-the-gallery-github-repository"></a>Ссылка на репозиторий коллекции на GitHub
Вот ссылка на общедоступный репозиторий GitHub: 

[https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning)

## <a name="use-case-overview"></a>Обзор варианта использования

Многие алгоритмы машинного обучения включают один или несколько элементов управления, которые называются гиперпараметрами. Эти элементы управления позволяют настраивать алгоритмы для оптимизации работы с будущими данными, которые измеряются в соответствии с пользовательскими метриками (такими, как точность, площадь под кривой, среднеквадратическая погрешность). При создании модели на основе учебных данных специалист по анализу и обработке данных должен настроить значения гиперпараметров, не зная, с какими тестовыми данными эта модель будет работать в будущем. Как на основе известных учебных данных настроить значения гиперпараметров, чтобы модель эффективно обрабатывала неизвестные тестовые данные? 
    
Популярный метод настройки гиперпараметров — *поиск в сетке* в сочетании с *перекрестной проверкой*. Перекрестная проверка — это метод, с помощью которого можно оценить, насколько хорошо модель, обученная на учебном наборе, выполняет прогнозирование по тестовому набору. При использовании этого метода мы сначала разделили набор данных на K сверток, а затем обучили алгоритм K раз путем циклического перебора. В переборе участвуют все свертки, кроме одной, которая называется контрольной сверткой. Мы вычислили среднее значение метрик K моделей и K контрольных сверток. Это среднее значение, называемое *оценкой эффективности путем перекрестной проверки*, зависит от значений гиперпараметров, используемых при создании K моделей. При настройке гиперпараметров мы выполняем поиск в пространстве потенциальных значений гиперпараметров, чтобы найти те из них, которые оптимизируют оценку эффективности путем перекрестной проверки. Поиск в сетке — это стандартная методика поиска. При этом методе поиска пространство потенциальных значений нескольких гиперпараметров рассматривается как векторное произведение наборов возможных значений отдельных гиперпараметров. 

Поиск в сетке с помощью перекрестной проверки может занимать много времени. Если в алгоритме используется пять гиперпараметров, для каждого из которых есть пять возможных значений, мы используем K = 5 сверток. Поиск в сетке выполняется путем обучения 5<sup>6</sup> = 15625 моделей. К счастью, поиск в сетке с помощью перекрестной проверки является процедурой с массовым параллелизмом, поэтому все модели можно обучить в параллельном режиме.

## <a name="prerequisites"></a>предварительным требованиям

* [Учетная запись Azure](https://azure.microsoft.com/free/) (доступны бесплатные пробные версии).
* Установленная копия [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Чтобы установить эту программу и создать учетные записи, выполните инструкции из [краткого руководства по установке и созданию](../service/quickstart-installation.md).
* В этом сценарии предполагается, что Azure ML Workbench работает в Windows 10 или MacOS с подсистемой Docker, установленной на локальном компьютере. 
* Чтобы выполнить сценарий в удаленном контейнере Docker, подготовьте виртуальную машину для обработки и анализа данных под управлением Ubuntu, следуя этим [инструкциям](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Рекомендуем использовать виртуальную машину с как минимум 8 ядрами и 28 ГБ памяти. Такими характеристиками обладают экземпляры виртуальных машин D4. 
* Чтобы выполнить этот сценарий в кластере Spark, подготовьте кластер Spark HDInsight, следуя этим [инструкциям](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters). Мы рекомендуем использовать кластер с такими конфигурациями на головном и рабочем узлах:
    - четыре рабочих узла;
    - восемь ядер;
    - 28 ГБ памяти.  
      
  Такими характеристиками обладают экземпляры виртуальных машин D4. 

     **Устранение неполадок.** В вашей подписке Azure может быть установлена квота на количество используемых ядер. Портал Azure не позволяет создать кластер с общим числом ядер, превышающим квоту. Чтобы найти квоту, на портале Azure перейдите в раздел "Подписки", щелкните подписку, которая использовалась для развертывания кластера, и выберите **Использование и квоты**. Обычно квоты определяются для каждого региона Azure, и вы можете развернуть кластер Spark в регионе с достаточным количеством свободных ядер. 

* Создайте учетную запись хранения Azure для хранения набора данных. Следуйте [инструкциям](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) по созданию учетной записи хранения.

## <a name="data-description"></a>Описание данных

Мы используем [набор данных TalkingData](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data). Этот набор данных содержит события из приложений на мобильных телефонах. Цель заключается в прогнозировании пола и возрастной категории пользователя мобильного телефона, если известна модель телефона и события, созданные пользователем за последнее время.  

## <a name="scenario-structure"></a>Структура сценария
Для этого сценария в репозитории GitHub есть несколько папок. Код и файлы конфигурации хранятся в папке **Code**, вся документация — в папке **Docs**, а все изображения — в папке **Images**. В корневой папке есть файл README, содержащий краткое описание сценария.

### <a name="getting-started"></a>Приступая к работе
Щелкните значок Azure Machine Learning Workbench, чтобы запустить Azure Machine Learning Workbench и создать проект на основе шаблона Distributed Tuning of Hyperparameters (Распределенная настройка гиперпараметров). Подробные инструкции по созданию нового проекта см. в [этом кратком руководстве](quickstart-installation.md).   

### <a name="configuration-of-execution-environments"></a>Настройка сред выполнения
Мы покажем вам, как выполнить код в удаленном контейнере Docker и в кластере Spark. Начнем с описания параметров, которые являются общими для обеих сред. 

Мы используем пакеты [scikit-learn](https://anaconda.org/conda-forge/scikit-learn), [xgboost](https://anaconda.org/conda-forge/xgboost) и [azure-storage](https://pypi.python.org/pypi/azure-storage), которые не предоставляются в используемом по умолчанию контейнере Docker для Azure Machine Learning Workbench. Для пакета azure-storage необходимо установить пакеты [cryptography](https://pypi.python.org/pypi/cryptography) и [azure](https://pypi.python.org/pypi/azure). Чтобы установить эти пакеты в контейнере Docker и на узлах кластера Spark, мы внесем изменения в файл conda_dependencies.yml:

    name: project_environment
    channels:
      - conda-forge
    dependencies:
      - python=3.5.2
      - scikit-learn
      - xgboost
      - pip:
        - cryptography
        - azure
        - azure-storage

Измененный файл conda\_dependencies.yml хранится в каталоге aml_config этого руководства. 

На следующих шагах мы подключим среду выполнения к учетной записи Azure. Щелкните меню "Файл" в левом верхнем углу AML Workbench. Выберите пункт "Открыть командную строку". Затем выполните в CLI следующую команду.

    az login

Вы получите такое сообщение:

    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code <code> to authenticate.

Перейдите к этой веб-странице, введите код и войдите с помощью учетной записи Azure. После выполнения этого шага выполните в CLI команду

    az account list -o table

и найдите идентификатор подписки Azure, к которой относится учетная запись рабочей области AML Workbench. Наконец, выполните в CLI следующую команду:

    az account set -s <subscription ID>

Это необходимо, чтобы завершить настройку подключения к подписке Azure.

В следующих двух разделах мы покажем, как завершить настройку удаленного контейнера Docker и кластера Spark.

#### <a name="configuration-of-remote-docker-container"></a>Настройка удаленного контейнера Docker

 Чтобы настроить удаленный контейнер Docker, выполните в CLI команду

    az ml computetarget attach remotedocker --name dsvm --address <IP address> --username <username> --password <password> 

с IP-адресом, именем пользователя и паролем виртуальной машины для обработки и анализа данных. IP-адрес виртуальной машины для обработки и анализа данных можно найти в разделе "Обзор" страницы этой виртуальной машины на портале Azure:

![IP-адрес виртуальной машины](media/scenario-distributed-tuning-of-hyperparameters/vm_ip.png)

#### <a name="configuration-of-spark-cluster"></a>Конфигурация кластера Spark

Чтобы настроить среду Spark, выполните в CLI команду

    az ml computetarget attach cluster --name spark --address <cluster name>-ssh.azurehdinsight.net  --username <username> --password <password> 

с именем кластера, именем пользователя SSH и паролем кластера. Имя пользователя SSH по умолчанию — `sshuser`, если вы не изменили его во время подготовки кластера. Имя кластера можно найти в разделе "Свойства" страницы кластера на портале Azure:

![Имя кластера](media/scenario-distributed-tuning-of-hyperparameters/cluster_name.png)

Чтобы использовать Spark как среду выполнения для распределенной настройки гиперпараметров, мы применим пакет spark-sklearn. Чтобы установить этот пакет при использовании среды выполнения Spark, мы внесли изменения в файл spark_dependencies.yml:

    configuration: 
      #"spark.driver.cores": "8"
      #"spark.driver.memory": "5200m"
      #"spark.executor.instances": "128"
      #"spark.executor.memory": "5200m"  
      #"spark.executor.cores": "2"
  
    repositories:
      - "https://mmlspark.azureedge.net/maven"
      - "https://spark-packages.org/packages"
    packages:
      - group: "com.microsoft.ml.spark"
        artifact: "mmlspark_2.11"
        version: "0.7.91"
      - group: "databricks"
        artifact: "spark-sklearn"
        version: "0.2.0"

Измененный файл spark\_dependencies.yml хранится в каталоге aml_config этого руководства. 

### <a name="data-ingestion"></a>Прием данных
В коде этого сценария предполагается, что данные хранятся в хранилище BLOB-объектов Azure. Сначала мы покажем, как скачать данные с сайта Kaggle на компьютер и отправить их в хранилище BLOB-объектов, а затем объясним, как считывать данные из хранилища BLOB-объектов. 

Чтобы скачать данные с сайта Kaggle, перейдите к [странице набора данных](https://www.kaggle.com/c/talkingdata-mobile-user-demographics/data) и нажмите кнопку Download (Скачать). Вам будет предложено войти в Kaggle. После входа вы будете снова перенаправлены на страницу набора данных. Затем скачайте первые семь файлов в левом столбце, выбрав их и нажав кнопку Download (Скачать). Общий размер скачанных файлов — 289 МБ. Чтобы отправить эти файлы в хранилище BLOB-объектов, создайте контейнер хранилища BLOB-объектов с именем dataset в своей учетной записи хранения. Чтобы сделать это, перейдите на страницу учетной записи хранения на портале Azure, нажмите кнопку "BLOB-объекты" и щелкните "+Контейнер". Укажите dataset в качестве имени и нажмите кнопку ОК. Эти шаги показаны на следующих снимках экрана.

![Щелкните "BLOB-объекты"](media/scenario-distributed-tuning-of-hyperparameters/open_blob.png)
![Щелкните "+Контейнер"](media/scenario-distributed-tuning-of-hyperparameters/open_container.png)

После этого выберите из списка контейнер набора данных и нажмите кнопку "Отправить". Через портал Azure можно одновременно отправить несколько файлов. В разделе "Отправить BLOB-объект" щелкните значок папки, выберите все файлы из набора данных, нажмите кнопку "Открыть", а затем — кнопку "Отправить". На следующем снимке экрана показаны все эти шаги.

![Отправка большого двоичного объекта](media/scenario-distributed-tuning-of-hyperparameters/upload_blob.png) 

Отправка файлов занимает несколько минут в зависимости от скорости подключения к Интернету. 

Для скачивания набора данных из хранилища BLOB-объектов в текущую среду выполнения в нашем коде используется [пакет SDK службы хранилища Azure](https://azure-storage.readthedocs.io/en/latest/). Скачивание выполняется с помощью функции load\_data() из файла load_data.py. Чтобы использовать этот код, необходимо заменить <ACCOUNT_NAME> и <ACCOUNT_KEY> именем и первичным ключом учетной записи хранения, в которой размещен набор данных. Имя учетной записи можно найти в левом верхнем углу страницы учетной записи хранения на портале Azure. Чтобы получить ключ учетной записи, щелкните "Ключи доступа" на странице учетной записи хранения на портале Azure (см. первый снимок экрана в разделе "Прием данных") и скопируйте первую длинную строку в столбце "Ключ":
 
![Ключ доступа](media/scenario-distributed-tuning-of-hyperparameters/access_key.png)

Следующий код из функции load_data() скачивает один файл:

    from azure.storage.blob import BlockBlobService

    # Define storage parameters 
    ACCOUNT_NAME = "<ACCOUNT_NAME>"
    ACCOUNT_KEY = "<ACCOUNT_KEY>"
    CONTAINER_NAME = "dataset"

    # Define blob service     
    my_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # Load blob
    my_service.get_blob_to_path(CONTAINER_NAME, 'app_events.csv.zip', 'app_events.csv.zip')

Обратите внимание, что файл load_data.py не нужно запускать вручную. Позже он вызывается из других файлов.

### <a name="feature-engineering"></a>Проектирование признаков
Код для вычисления всех признаков находится в файле feature\_engineering.py. Файл feature_engineering.py не нужно запускать вручную. Позже он будет вызываться из других файлов.

Мы создадим несколько наборов признаков:
* прямое кодирование торговой марки и модели мобильного телефона (функция one\_hot\_brand_model);
* доля событий, создаваемых пользователем в каждый день недели (функция weekday\_hour_features);
* доля событий, создаваемых пользователем каждый час (функция weekday\_hour_features);
* доля событий, создаваемых пользователем для каждого сочетания дня недели и часа (функция weekday\_hour_features);
* доля событий, создаваемых пользователем в каждом приложении (функция one\_hot\_app_labels);
* доля событий, создаваемых пользователем для каждой метки приложения (функция one\_hot\_app_labels);
* доля событий, создаваемых пользователем для каждой категории приложений (функция text\_category_features);
* признаки-индикаторы для категорий приложений, с помощью которых создавались события (функция one\_hot_category).

Эти признаки были созданы на основе фрагмента кода Kaggle [A linear model on apps and labels](https://www.kaggle.com/dvasyukova/a-linear-model-on-apps-and-labels) (Линейная модель на основе приложений и меток).

Для вычисления этих признаков требуется значительный объем памяти. Сначала мы попытались вычислить признаки в локальной среде с 16 ГБ ОЗУ. Мы смогли вычислить первые четыре набора признаков, но при вычислении пятого набора возникла ошибка "Недостаточно памяти". Вычисление первых четырех наборов признаков выполнялось в файле singleVMsmall.py с помощью команды 

     az ml experiment submit -c local .\singleVMsmall.py   

в окне CLI.

Так как ресурсов локальной среды недостаточно для вычисления всех наборов признаков, мы переключились на удаленную виртуальную машину для обработки и анализа данных с большим объемом памяти. Эта операция на виртуальной машине для обработки и анализа данных выполняется в контейнере Docker, управляемом с помощью AML Workbench. С помощью этой виртуальной машины для обработки и анализа данных мы смогли вычислить все признаки, а также обучить модели и настроить гиперпараметры (см. следующий раздел). Файл singleVM.py завершил вычисление признаков и моделирование кода. В следующем разделе мы покажем, как запустить файл singleVM.py на удаленной виртуальной машине для обработки и анализа данных. 

### <a name="tuning-hyperparameters-using-remote-dsvm"></a>Настройка гиперпараметров с помощью удаленной виртуальной машины для обработки и анализа данных
Мы используем реализацию [xgboost](https://anaconda.org/conda-forge/xgboost) [1] градиентного бустинга деревьев. Для настройки гиперпараметров xgboost также используется пакет [scikit-learn](http://scikit-learn.org/). Xgboost не входит в пакет scikit-learn, но реализует API scikit-learn и поэтому может использоваться вместе с функциями scikit-learn, предназначенными для настройки гиперпараметров. 

Xgboost состоит из восьми гиперпараметров, описанных [здесь](https://github.com/dmlc/xgboost/blob/master/doc/parameter.md):
* n_estimators;
* max_depth;
* reg_alpha;
* reg_lambda;
* colsample\_by_tree;
* learning_rate;
* colsample\_by_level;
* subsample.
* objective.  
 
Сначала мы воспользуемся удаленной виртуальной машиной для обработки и анализа данных и настроим гиперпараметры из небольшой сетки потенциальных значений:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1],'learning_rate': [0.1], 'colsample_bylevel': [0.1,], 'subsample': [0.5]}]  

Эта сетка содержит четыре комбинации значений гиперпараметров. Мы используем перекрестную проверку 5 сверток, в результате чего xgboost будет выполняться 4 x 5 = 20 раз. Для измерения эффективности моделей мы используем метрику функции логистических потерь с отрицательным значением. Приведенный ниже код находит в сетке значения гиперпараметров, которые максимизируют отрицательные логистические потери с перекрестной проверкой. В коде эти значения также используются для обучения итоговой модели с полным набором учебных данных:

    clf = XGBClassifier(seed=0)
    metric = 'neg_log_loss'
    
    clf_cv = GridSearchCV(clf, tuned_parameters, scoring=metric, cv=5, n_jobs=8)
    model = clf_cv.fit(X_train,y_train)

После создания модели мы сохраним результаты настройки гиперпараметров. Мы используем API ведения журнала AML Workbench для сохранения оптимальных значений гиперпараметров и соответствующей оценки перекрестной проверки функции логистических потерь с отрицательным значением:

    from azureml.logging import get_azureml_logger

    # initialize logger
    run_logger = get_azureml_logger()

    ...

    run_logger.log(metric, float(clf_cv.best_score_))

    for key in clf_cv.best_params_.keys():
        run_logger.log(key, clf_cv.best_params_[key]) 

Также мы создадим файл sweeping_results.txt с отрицательными значениями логистических потерь с перекрестной проверкой для всех сочетаний значений гиперпараметров в сетке:

    if not path.exists('./outputs'):
        makedirs('./outputs')
    outfile = open('./outputs/sweeping_results.txt','w')

    print("metric = ", metric, file=outfile)
    for i in range(len(model.grid_scores_)):
        print(model.grid_scores_[i], file=outfile)
    outfile.close()

Этот файл хранится в специальном каталоге ./outputs. Позже мы покажем, как его скачать.  

 Перед первым выполнением файла singleVM.py на удаленной виртуальной машине для обработки и анализа данных мы создадим контейнер Docker, выполнив команду 

    az ml experiment prepare -c dsvm

в окне CLI. Создание контейнера Docker займет несколько минут. После этого мы выполним файл singleVM.py на виртуальной машине для обработки и анализа данных:

    az ml experiment submit -c dsvm .\singleVM.py

На виртуальной машине для обработки и анализа данных с 8 ядрами и 28 ГБ памяти выполнение этой команды занимает 1 час 38 минут. Зарегистрированные значения можно просмотреть в окне журнала выполнения AML Workbench:

![Журнал выполнения](media/scenario-distributed-tuning-of-hyperparameters/run_history.png)

По умолчанию в окне журнала выполнения отображаются значения и графики для первых 1–2 зарегистрированных значений. Чтобы просмотреть полный список выбранных значений гиперпараметров, щелкните значок настроек, обведенный красным на предыдущем снимке экрана. Теперь выберите гиперпараметры, которые нужно отобразить в таблице. Чтобы выбрать графики, которые будут отображаться в верхней части окна журнала выполнения, щелкните значок настроек, обведенный синим, и выберите графики из списка. 

Выбранные значения гиперпараметров также можно просмотреть в окне Run Properties (Свойства запуска): 

![Свойства запуска](media/scenario-distributed-tuning-of-hyperparameters/run_properties.png)

В правом верхнем углу окна Run Properties (Свойства запуска) есть раздел Output Files (Выходные файлы) со списком всех файлов, которые были созданы в папке .\output. Чтобы скачать файл sweeping\_results.txt из этой папки, выберите его и нажмите кнопку скачивания. Файл sweeping_results.txt должен содержать следующие выходные данные:

    metric =  neg_log_loss
    mean: -2.29096, std: 0.03748, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28712, std: 0.03822, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 3}
    mean: -2.28706, std: 0.03863, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 300, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}
    mean: -2.28530, std: 0.03927, params: {'colsample_bytree': 1, 'learning_rate': 0.1, 'subsample': 0.5, 'n_estimators': 400, 'reg_alpha': 1, 'objective': 'multi:softprob', 'colsample_bylevel': 0.1, 'reg_lambda': 1, 'max_depth': 4}

### <a name="tuning-hyperparameters-using-spark-cluster"></a>Настройка гиперпараметров с помощью кластера Spark
Кластер Spark нужен для масштабирования настройки гиперпараметров и использования сетки большего размера. Вот наша новая сетка:

    tuned_parameters = [{'n_estimators': [300,400], 'max_depth': [3,4], 'objective': ['multi:softprob'], 'reg_alpha': [1], 'reg_lambda': [1], 'colsample_bytree': [1], 'learning_rate': [0.1], 'colsample_bylevel': [0.01, 0.1], 'subsample': [0.5, 0.7]}]

Эта сетка содержит 16 комбинаций значений гиперпараметров. Так как мы используем перекрестную проверку 5 сверток, xgboost будет выполняться 16 x 5 = 80 раз.

В пакете scikit-learn отсутствует собственная поддержка настройки гиперпараметров с помощью кластера Spark. К счастью, пакет [spark-sklearn](https://spark-packages.org/package/databricks/spark-sklearn) от Databricks восполняет этот пробел. Этот пакет содержит функцию GridSearchCV, API которой практически аналогичен API функции GridSearchCV в пакете scikit-learn. Чтобы использовать пакет spark-sklearn и настроить гиперпараметры с помощью Spark, требуется создать контекст Spark.

    from pyspark import SparkContext
    sc = SparkContext.getOrCreate()

Затем мы заменим 

    from sklearn.model_selection import GridSearchCV

на 

    from spark_sklearn import GridSearchCV

Также мы заменим вызов функции GridSearchCV из пакета scikit-learn на вызов аналогичной функции из spark-sklearn:

    clf_cv = GridSearchCV(sc = sc, param_grid = tuned_parameters, estimator = clf, scoring=metric, cv=5)

Итоговый код для настройки гиперпараметров с помощью Spark находится в файле distributed\_sweep.py. Разница между файлами singleVM.py и distributed_sweep.py заключается в определении сетки и четырех дополнительных строках кода. Обратите внимание, что благодаря службам AML Workbench код ведения журнала не меняется при изменении среды выполнения с удаленной виртуальной машины для обработки и анализа данных на кластер Spark.

Перед первым выполнением файла distributed_sweep.py в кластере Spark необходимо установить в нем пакеты Python. Это можно сделать, выполнив команду 

    az ml experiment prepare -c spark

в окне CLI. Установка пакетов занимает несколько минут. После этого мы запускаем файл distributed_sweep.py в кластере Spark:

    az ml experiment submit -c spark .\distributed_sweep.py

Выполнение этой команды в кластере Spark с 6 рабочими узлами и 28 ГБ памяти занимает 1 час 6 минут. Результаты настройки гиперпараметров можно отслеживать в Azure Machine Learning Workbench, так же как и работу виртуальной машины для обработки и анализа данных. (Здесь доступны журналы, оптимальные значения гиперпараметров и файл sweeping_results.txt.)

### <a name="architecture-diagram"></a>Схема архитектуры

На следующей схеме показан полный рабочий процесс. ![Архитектура](media/scenario-distributed-tuning-of-hyperparameters/architecture.png) 

## <a name="conclusion"></a>Заключение 

В этом примере мы показали, как с помощью Azure Machine Learning Workbench настроить гиперпараметры на удаленных виртуальных машинах и в кластере Spark. Мы узнали, что Azure Machine Learning Workbench предоставляет средства для простой настройки сред выполнения, а также позволяет легко переключаться между ними. 

## <a name="references"></a>Ссылки

[1] T. Чен (T. Chen) и К. Гестрин (C. Guestrin). [XGBoost: A Scalable Tree Boosting System](https://arxiv.org/abs/1603.02754) (XGBoost: масштабируемая система бустинга деревьев). Конференция KDD, 2016 г.




