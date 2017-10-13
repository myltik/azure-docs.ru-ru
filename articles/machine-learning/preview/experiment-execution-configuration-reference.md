---
title: "Файлы конфигурации выполнения Azure Machine Learning Workbench"
description: "В этом документе описаны параметры конфигурации для выполнения экспериментов в Azure ML Workbench."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: e1356439385cc7fe66985bd2b84e4121386ec23d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-workbench-execution-configuration-files"></a>Файлы конфигурации выполнения Azure Machine Learning Workbench

Когда вы отправляете скрипт в Azure ML Workbench, реакция на него и режим выполнения зависят от файлов в папке **aml_config**. Эта папка находится в корневой папке проекта. Важно понимать, что содержат эти файлы, чтобы получить желаемый результат.

Ниже перечислены важные файлы из этой папки.
- conda_dependencies.yml
- spark_dependencies.yml
- Файлы целевых объектов вычисления
    - \<имя_целевого_объекта_вычисления> .compute
- Файлы конфигурации запуска
    - \<имя_конфигурации_запуска>.runconfig

>[!NOTE]
>Обычно используются один файл целевого объекта вычислений и один файл конфигурации запуска для каждого целевого объекта вычислений, который вы создаете. Но вы можете создавать такие файлы дополнительно и использовать несколько файлов конфигурации для одного целевого объекта вычислений.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
[Файл среды conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) указывает версию среды выполнения Python и пакеты, от которых зависит ваш код. Если приложение Azure ML Workbench выполняет скрипт в контейнере Docker или кластере HDInsight, оно создает [среду conda](https://conda.io/docs/using/envs.html) для запуска этого скрипта. 

В этом файле вы можете указать пакеты Python, необходимые для выполнения скрипта. Служба выполнения Azure ML Workbench создает среду conda в образе Docker с учетом этого списка зависимостей. Список пакетов должен быть доступен для подсистемы выполнения. Поэтому следует указывать пакеты в одном из таких каналов:

* [continuum.io](https://anaconda.org/conda-forge/repo);
* [PyPI](https://pypi.python.org/pypi)
* общедоступная конечная точка (URL-адрес);
* путь к локальному файлу;
* другие расположения, доступные для подсистемы выполнения.

>[!NOTE]
>Если приложение Azure ML Workbench работает в кластере HDInsight, оно создает отдельную среду conda для запуска вашего скрипта. Это позволяет различным пользователям использовать разные среды Python на одном кластере.  

Ниже приведен пример типичного файла **conda_dependencies.yml**.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench использует одну среду conda, не перестраивая ее, если файл **conda_dependencies.yml** не изменялся. Но при изменении любых параметров в этом файле перестраивается образ Docker.

>[!NOTE]
>Если вы направляете выполнение в _локальный_ контекст вычислений, файл **conda_dependencies.yml** **не используется**. В этом случае зависимости пакетов для локальной среды Python в Azure ML Workbench необходимо установить вручную.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Этот файл указывает имя приложения Spark, когда вы передаете сценарий PySpark и пакеты Spark, которые необходимо установить. Также вы можете указать любой открытый репозиторий Maven или пакет Spark, расположенный в открытых репозиториях Maven.

Пример:

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Параметры настройки кластера, например размер рабочего потока и количество ядер, следует указывать в разделе configuration файла spark_dependecies.yml 

>[!NOTE]
>Если вы выполняете скрипт в среде Python, файл *spark_dependencies.yml* игнорируется. Он учитывается только при запуске в среде Spark (на Docker или в кластере HDInsight).

## <a name="run-configuration"></a>Конфигурация запуска
Чтобы определить конфигурацию запуска, нужны два файла. Обычно они создаются с помощью команды CLI. Но вы можете клонировать уже существующие файлы, переименовать их и изменить содержимое.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password> --type remotedocker

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> -type cluster
```

Эта команда создает пару файлов для указанного целевого объекта вычислений. Предположим, вы используете целевой объект вычислений с именем _foo_. Эта команда создаст файлы _foo.compute_ и _foo.runconfig_ в папке **aml_config**.

>[!NOTE]
> Имена _local_ и _docker_ для файлов конфигурации запуска выбраны произвольно. Azure ML Workbench упрощает вам задачу, автоматически добавляя эти две конфигурации запуска при создании пустого проекта. Вы можете переименовать файлы <run configuration name>.runconfig, предоставленные вам в шаблоне проекта, или создать новые с любыми именами.

### <a name="compute-target-namecompute"></a>\<имя_целевого_объекта_вычисления> .compute
В файле _\<имя_целевого_объекта_вычисления>.compute_ указаны сведения о подключении к целевому объекту вычислений и его конфигурации. Информация в нем хранится в виде списка пар "имя — значение". Ниже перечислены поддерживаемые параметры.

**type**. Тип вычислительной среды. Поддерживаемые значения:
  - local
  - docker
  - remotedocker
  - cluster

**baseDockerImage**. Образ Docker, который используется для запуска скрипта Python или PySpark. Значение по умолчанию — _microsoft/mmlspark:plus-0.7.91_. Мы предлагаем еще один образ: _microsoft/mmlspark:plus-gpu-0.7.91_, который поддерживает использование графического процессора на хост-компьютере (при наличии графического процессора).

**address**. IP-адрес или полное доменное имя виртуальной машины или головного узла кластера HDInsight.

**username**. Имя пользователя для доступа по протоколу SSH к виртуальной машине или головному узлу HDInsight.

**password**. Зашифрованный пароль для SSH-подключения.

**sharedVolumes**. Этот флаг указывает, что подсистема выполнения должна использовать функцию общего тома Docker для отправки и получения файлов проекта. Включение этого флага может ускорить выполнение, поскольку Docker сможет получить доступ к проектам напрямую, без дополнительного копирования. При выполнении в ОС Windows этот флаг лучше отключать (установить значение _false_), поскольку совместное использование томов в Docker для Windows работает нестабильно. При работе в ОС macOS или Linux установите значение _true_.

**nvidiaDocker**. Если этот флаг имеет значение _true_, служба выполнения Azure ML Workbench будет использовать для запуска образа Docker команду _nvidia-docker_, а не стандартную команду _docker_. Обработчик _nvidia-docker_ разрешает контейнеру Docker использовать графический процессор.  Этот параметр является обязательным, если вы хотите предоставить графический процессор для контейнера Docker. Команда _nvidia-docker_ поддерживается только в ОС Linux. Например, _nvidia-docker_ входит в поставку виртуальной машины для обработки и анализа данных на основе Linux в Azure. В ОС Windows команда _nvidia-docker_ пока не поддерживается.

**nativeSharedDirectory**. Этот параметр указывает базовый каталог (например, _~/.azureml/share/_), в котором файлы будут сохраняться между последовательными запусками для одного целевого объекта вычислений. Если этот параметр используется при работе с контейнером Docker, необходимо установить значение true для параметра _sharedVolumes_. В противном случае произойдет сбой выполнения.

### <a name="run-configuration-namerunconfig"></a>\<имя_конфигурации_запуска>.runconfig
В файле _\<имя_конфигурации_запуска>.runconfig_ задается поведение выполнения для Azure ML Workbench. Вы можете настроить поведение выполнения, например отслеживание журнала выполнения, выбор целевого объекта вычислений и множество других действий. Имена файлов конфигурации запуска будут перечислены в раскрывающемся списке контекста выполнения в приложении Azure ML Workbench для рабочего стола.

**ArgumentVector**. В этом разделе указывается скрипт, который должен выполняться в этом запуске, и параметры для него. Допустим, в файле <run configuration name>.runconfig содержится следующий фрагмент кода: 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
Тогда команда _az ml experiment submit foo.runconfig_ автоматически запустит файл _myscript.py_, передаст ему параметр 234 и установит флаг --verbose.

**Target**. Этот параметр содержит имя файла _.compute_, на который ссылается файл _runconfig_. Обычно здесь указывается файл _foo.compute_, но вы можете изменить его, указав другой целевой объект вычислений.

**EnvironmentVariables**. Этот раздел позволяет пользователям определить переменные среды для запуска. Переменные среды указываются в виде списка пар "имя — значение" в следующем формате:
```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

Эти переменные среды затем можно использовать в пользовательском коде. Например, такой код Phyton выводит значение переменной среды с именем EXAMPLE_ENV_VAR:
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework**. Это свойство указывает, что приложение Azure ML Workbench должно запустить сеанс Spark для выполнения скрипта. По умолчанию используется значение _PySpark_. Задайте для него значение _Python_, если вы не используете код PySpark. Это позволит запустить задание быстрее и с меньшей нагрузкой.

**CondaDependenciesFile**. Это свойство указывает файл из папки *aml_config*, который задает зависимости среды conda. Если указано значение _null_, используется файл по умолчанию **conda_dependencies.yml**.

**SparkDependenciesFile**. Это свойство указывает файл из папки **aml_config**, который задает зависимости для Spark. Если указано значение _null_, используется файл по умолчанию **spark_dependencies.yml**.

**PrepareEnvironment**. Если для этого свойства задано значение _true_, служба выполнения подготовит среду conda с учетом зависимостей conda, указанных для начального запуска. Это свойство учитывается только при выполнении в среде Docker. Этот параметр никак не используется в локальной (_local_) среде. 

**TrackedRun**. Этот флаг сообщает службе выполнения, нужно ли отслеживать этот запуск в инфраструктуре журнала выполнения Azure ML Workbench. Значение по умолчанию — _true_. 

**UseSampling**. Этот параметр определяет, нужно ли использовать для этого запуска действующие примеры наборов данных для источников данных.__ Если указано значение _false_, источники данных получают и используют все доступные данные из хранилища данных. Если указано значение _true_, используются действующие примеры. С помощью параметра **DataSourceSettings** пользователи могут переопределить действующий пример и указать, какие примеры наборов данных нужно использовать. 

**DataSourceSettings**. В этом разделе конфигурации указываются параметры источника данных. Здесь пользователь может указать, какие существующие примеры данных для конкретного источника данных следует использовать в процессе выполнения. 

Приведенный ниже элемент конфигурации указывает, что для источника данных с именем MyDataSource используется пример с именем MySample.
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions**. Определяет подстановки источников данных, которые позволяют перейти от одного источника данных к другому без изменения кода. Например, пользователь может переключиться с ограниченной выборки в локальном файле на исходный набор данных, который хранится в BLOB-объекте Azure, просто изменив ссылку на источник данных. Если используется подстановка, Azure ML Workbench использует источник данных, указанный в подстановке, при любых обращениях к источникам данных и выполнении пакетов подготовки данных.

Следующий пример конфигурации заменяет все ссылки mylocal.datasource в источниках данных и пакетах подготовки данных Azure ML Workbench новым значением myremote.dsource. 
 
```
DataSourceSubstitutions:
    myocal.dsource: myremote.dsource
```

Если используется приведенная выше подстановка, следующий образец кода будет считывать данные из myremote.dsource, а не mylocal.dsource, при этом пользователям не нужно вносить изменения в код.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о [настройке среды выполнения](experiment-execution-configuration.md)
