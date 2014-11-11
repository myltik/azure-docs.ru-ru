<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning Sample: Network intrusion detection | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Пример машинного обучения Azure: обнаружение сетевого вторжения

*Пример эксперимента, связанный с этой моделью в ML Studio, можно найти в разделе **ЭКСПЕРИМЕНТЫ** на вкладке **ПРИМЕРЫ**. Имя эксперимента:*

    Sample Experiment - Network Intrusion Detection - Development

## Описание проблемы

В демонстрационном примере используются различные сетевые функции для обнаружения сетевых операций, которые являются частью вторжения/атаки. Эта проблема двоичной классификации не является слишком трудной, так как функции являются хорошо прогнозируемыми по классу, а классы равномерно сбалансированы. Так как несколько функций являются частью метки, важно удалить все части метки до обучения классификатора.

## Данные

1.  Данные (из состязания KDD cup 1999) включают в себя наборы данных для обучения и тестирования. Уровни точности классификации в наборе данных для обучения являются исторически более высокими, чем набор данных для тестирования, так как в тестовую выборку введены новые сетевые вторжения. Набор данных для обучения имеет приблизительно 126 000 строк и 43 столбца, включая метки. Так как три столбца являются частью информации о метке, для обучения модели имеется 40 столбцов. Эти 40 столбцов, в основном, являются численными с несколькими строковыми / категориальными функциями. Тестовые данные содержат приблизительно 22500 тестовых примеров (с теми же 43 столбцами для данных для обучения).
2.  Данные были загружены в общедоступный BLOB-объект для обеспечения простого доступа. AzureML содержит модули чтения для считывания данных из различных хранилищ, например, BLOB-объектов с помощью нескольких щелчков мыши (никакой код создавать не нужно).

## Модель

1.  Первый шаг в модели должен представлять собой создание условия для данных для классификации в будущем. Столбец «Класс» содержит метки для прогнозирования. Эти метки являются либо «нормальными»(вторжение отсутствует), либо содержат имя атаки. Для некоторых атак в данных обучения нет большого количества примеров, а в наборе тестовых данных существуют новые атаки. Таким образом, мы преобразуем эти мультиклассовые метки в двоичные метки класса для моделирования проблемы. Studio содержит встроенный модуль, который позволяет упростить этот шаг предварительной обработки. Модуль индикаторных значений используется для преобразования в двоичную форму меток классов, а затем модуль столбцов проекта используется для выбора этого столбца метки класса в двоичном виде (исключая исходный столбец метки класса).
2.  В рамках разработки этого эксперимента благодаря выбору/корреляции функций, мы видим, что многие функции сильно коррелируют с меткой. Это является распространенной ситуацией в синтетических наборах данных. Облачная система ML обеспечивает такую визуализацию за несколько простых щелчков мышью. Выберите параметр визуализации на выходе модуля проекта и щелкните заголовок любой из функций, чтобы активировать визуализатор, а затем выберите метку класса в раскрывающемся списке.
3.  Так как с меткой сильно коррелирует небольшое количество функций, мы будет тестировать комбинации линейных и нелинейных классификаторов с выбором функции и без выбора, чтобы определить наилучшую модель. Такое сравнение выполняется с помощью модуля анализа модели.

## Результат

1.  Нелинейный классификатор (увеличивающееся дерево решений) работает чуть лучше, чем линейный классификатор (логистическая регрессия):

![][0]

Теперь мы хотим сравнить с увеличивающимся деревом решений с выбором функции. Характеристики классификации примерно похожи, но увеличивающееся дерево решений на всех функциях работает немного лучше, и будет использоваться для рабочего процесса выставления оценок. Следует отметить, что такой AUC с высокой классификацией является типичным для этого набора данных.

![][1]

<!-- Removed until this part is fixed ## Operationalization ##   We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio:   1. First step is to save the learned model (by right clicking on the classifier module output)  1. Now create a new experiment and search for saved model and drop it in the panel for new experiment  1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment  1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service  1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio    -->

  [0]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
  [1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
