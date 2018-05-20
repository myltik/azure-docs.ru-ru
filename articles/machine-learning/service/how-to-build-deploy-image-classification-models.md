---
title: Создайте и разверните модель классификации изображений, используя пакет Машинного обучения Azure для компьютерного зрения.
description: Узнайте, как создавать, обучать, тестировать и развертывать модель классификации изображений компьютерного зрения, используя пакет Машинного обучения Azure для компьютерного зрения.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: bd9f01e76c68fa41616818251b5b54553059cbcc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Построение и развертывание моделей классификации изображений с помощью Машинного обучения Azure

В этой статье вы узнаете, как обучать, тестировать и развертывать модели классификации изображений с помощью **пакета Машинного обучения Azure для компьютерного зрения** (AMLPCV). 

Множество проблем в области компьютерного зрения можно решить, используя классификацию изображений. В частности, можно решить следующие проблемы создания моделей:
+ _Присутствует ли объект на изображении? Например, "собака", "автомобиль", "корабль" и т. д._
+ _Какой класс тяжести глазной болезни обнаруживается при сканировании сетчатки пациента?_

При создании и развертывании модели с помощью AMLPCV выполните следующие действия:
1. Создание набора данных.
2. Визуализации изображения и создание заметок.
3. Изображение с дополнительным содержимым.
4. Определение модели глубокой нейронной сети (DNN).
5. Обучение классификатора.
6. Оценка и визуализация.
7. Развертывание веб-службы.
8. Нагрузочное тестирование веб-службы.

[CNTK](https://www.microsoft.com/cognitive-toolkit/) используется как платформа глубокого обучения, которое выполняется локально на компьютере с графическим процессором, например [виртуальная машина для обработки и анализа данных для глубокого обучения](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), а в развертывании используется Operationalization CLI Машинного обучения Azure.

Ознакомьтесь со [справочной документацией](https://aka.ms/aml-packages/vision) с подробным описанием каждого модуля и класса.

## <a name="prerequisites"></a>предварительным требованиям

1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

1. Необходимо настроить и установить следующие учетные записи и приложения:
   - Учетная запись службы "Экспериментирование в Машинном обучении Azure". 
   - Учетная запись Управления моделями Машинного обучения Azure.
   - Установленное приложение Azure Machine Learning Workbench.

   Если они еще не созданы или не установлены, следуйте инструкциям в статье [Краткое руководство. Установка и начало работы со службами Машинного обучения Azure](../service/quickstart-installation.md). 

1. Необходимо установить пакет Машинного обучения Azure для компьютерного зрения. Узнайте, как [установить этот пакет здесь](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Демонстрационные данные и записная книжка

### <a name="get-the-jupyter-notebook"></a>Получение Jupyter Notebook

Скачайте записную книжку, чтобы самостоятельно запускать примеры, описанные здесь.

> [!div class="nextstepaction"]
> [Получение Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Загрузка демонстрационных данных

В следующем примере используется набор данных, состоящий из 63 изображений предметов посуды. Каждое изображение помечено как принадлежащее к одному из четырех разных классов (блюдо, чашка, столовые приборы, тарелка). Мы уменьшили размер изображений, чтобы этот пример можно было быстро выполнить. На практике необходимо указать по крайней мере 100 изображений каждого класса. Все изображения находятся в подкаталогах папки *../sample_data/imgs_recycling/* с именами bowl, cup, cutlery и plate.

![Набор данных службы "Машинное обучение Azure"](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>Контекст хранилища

Контекст хранилища используется для определения того, где будут храниться различные выходные файлы, такие как дополненные изображения или файлы модели DNN. Дополнительные сведения о контекстах хранилища см. в [документации по StorageContext](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test). 

Как правило, содержимое хранилища необходимо задавать явно. Тем не менее, чтобы избежать ограничения по размеру проекта на уровне 25 МБ, наложенного Azure Machine Learning Workbench, установите для каталога выходных данных для пакета Машинного обучения Azure Machine для компьютерного зрения расположение за пределами проекта Машинного обучения Azure (../../../../cvtk_output). Не забудьте удалить каталог cvtk_output, когда он перестанет использоваться.


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>Создание набора данных

После импорта зависимостей и установки контекста хранилища можно создать объект набора данных.

Чтобы создать этот объект с помощью пакета Машинного обучения Azure для компьютерного зрения, укажите корневой каталог изображений на локальном диске. Этот каталог должен соответствовать той же общей структуре, что и набор данных посуды, то есть содержать подкаталоги с фактическими изображениями:
- root
    - label 1
    - label 2
    - ...
    - label n
  
Для обучения модели классификации изображений для другого набора данных просто измените корневой путь `dataset_location` в следующем коде, чтобы указать на другие изображения.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

Объект набора данных обеспечивает возможность скачивания изображений с помощью [API Bing для поиска изображений](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Поддерживаются два типа поисковых запросов: 
+ обычные текстовые запросы;
+ запросы URL-адресов изображений.

Эти запросы вместе с меткой класса должны быть предоставлены внутри текстового файла с кодировкой JSON. Например: 

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Кроме того, вы должны явно создать контекстный объект с ключом API Bing для поиска изображений. Для этого требуется подписка API Bing для поиска изображений.

## <a name="visualize-and-annotate-images"></a>Визуализация изображений и добавление заметок к ним

Вы можете визуализировать изображения и корректировать метки в объекте набора данных с помощью следующего мини-приложения. 

Если вы столкнулись с ошибкой Widget Javascript not detected (Мини-приложение Javascript не обнаружено), запустите эту команду, чтобы решить проблему:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Набор данных службы "Машинное обучение Azure"](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Дополнение изображений

Модуль [`augmentation`](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) предоставляет функциональные возможности для расширения объекта набора данных с использованием всех преобразований, описанных в библиотеке [imgaug](https://github.com/aleju/imgaug). Преобразования изображений могут быть сгруппированы в одном конвейере, и в этом случае все преобразования одновременно применяются к каждому изображению. 

Если вы хотите применить различные шаги дополнения отдельно или любым другим способом, вы можете определить несколько конвейеров и передать их в функцию *augment_dataset*. Дополнительные сведения и примеры дополнения изображений см. в документации [imgaug](https://github.com/aleju/imgaug).

Добавление дополненных изображений в обучающий набор особенно полезно для небольших наборов данных. Так как процесс обучения DNN замедляется из-за увеличения количества обучающих изображений, рекомендуется начинать эксперименты без дополнений.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Определения моделей DNN

Этот пакет поддерживает следующие предварительно обученные модели глубокой нейронной сети. 
+ AlexNet
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

Эти глубокие нейронные сети могут использоваться в качестве классификатора или характеризатора. 

Дополнительные сведения о сетях см. [здесь](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), а базовые сведения о переносе обучения см. [здесь](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Параметры классификации изображения по умолчанию для этого пакета: 224 x 224 пикселей и Resnet-18 DNN. Эти параметры были выбраны, так как они хорошо работают по широкому кругу задач. Зачастую точность можно повысить, например путем увеличения разрешения изображения до 500 x 500 пикселей или выбора более глубокой модели (Resnet-50). Однако изменение параметров могут привести к значительному увеличению времени обучения. Ознакомьтесь со статьей о [повышении точности](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Обучение классификатора

Вы можете выбрать один из следующих методов для предварительно обученной DNN.

  - **Уточнение DNN**, которое обучает DNN выполнять непосредственную классификацию. Обучение DNN происходит медленно, но обычно приводит к лучшим результатам, так как во время обучения можно улучшить качество всей сети, чтобы обеспечить максимальную точность.

  - **Присвоение признаков DNN**, которое запускает DNN как есть, чтобы получить представление изображений более низкого разрешения (512, 2048 или 4096-битные данные с плавающей запятой). Затем это представление используется в качестве выходных данных для обучения отдельного классификатора. Так как DNN не изменяется, этот подход намного быстрее по сравнению с уточнением DNN, но менее точен. Тем не менее обучение внешнего классификатора, такого как линейный метод опорных векторов (SVM) (как показано в следующем коде), может обеспечить надежные базовые показатели и помочь понять путь решения проблемы.
  
Для визуализации прогресса обучения можно использовать TensorBoard. Чтобы активировать TensorBoard:
1. Добавьте параметр `tensorboard_logdir=PATH`, как показано в следующем коде:
1. Запустите клиент TensorBoard с помощью команды `tensorboard --logdir=PATH` в новом окне консоли.
1. Откройте веб-браузер, как требуется для TensorBoard, по умолчанию — localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Оценка и визуализация производительности модели

Вы можете оценить производительность обучаемой модели на независимом тестовом наборе данных с помощью оценочного модуля. Ниже перечислены некоторые метрики оценки, которые вычисляются:
 
+ Точность (по умолчанию усредненная по классу).
+ Кривая PR.
+ Кривая ROC.
+ Область под кривой.
+ Матрица неточностей


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Набор данных службы "Машинное обучение Azure"](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Набор данных службы "Машинное обучение Azure"](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>Ввод в эксплуатацию: развертывание и использование

Ввод в эксплуатацию — это процесс публикации моделей и кода в виде веб-служб и их использование для получения бизнес-результатов. 

После обучения модели ее можно развернуть в качестве веб-службы для использования с помощью [интерфейса командной строки службы "Машинное обучение Azure"](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Модели можно развернуть на локальном компьютере или в контейнере Службы контейнеров Azure (ACS). Используя ACS, вы можете масштабировать веб-службу вручную или при помощи функции автомасштабирования.

**Вход с помощью Azure CLI**

Используя учетную запись [Azure](https://azure.microsoft.com/) с действительной подпиской, войдите в систему, выполнив следующую команду CLI:
<br>`az login`

+ Чтобы переключить подписку Azure, используйте команду:
<br>`az account set --subscription [your subscription name]`

+ Для просмотра текущей учетной записи управления моделью используйте команду:
  <br>`az ml account modelmanagement show`

**Создание и настройка среды развертывания**

Вам нужно установить среду развертывания только один раз. Если у вас ее еще нет, настройте среду развертывания, используя [эти инструкции](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Правильно выполните требуемые шаги установки в локальную среду или кластер.
+ Локальные развертывания поддерживаются для Linux и Windows 10, но не для виртуальной машины Windows для обработки и анализа данных или виртуальной машины для глубокого обучения. 
+ Среда развертывания кластера поддерживается для Linux и Windows. 

Для просмотра среды активного развертывания используйте следующую команду CLI:
<br>`az ml env show`
   
Пример команды Azure CLI для создания и установки среды развертывания

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Управление веб-службами и развертываниями

Следующие API используются для развертывания моделей в виде веб-служб, управления этими веб-службами и развертываниями.

|Задача|API|
|----|----|
|Создание объекта развертывания|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Развертывание веб-службы|`deploy_obj.deploy()`|
|Оценка изображения|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Удаление веб-службы|`deploy_obj.delete()`|
|Создание образа docker без веб-службы|`deploy_obj.build_docker_image()`|
|Получение списка имеющихся развертываний|`AMLDeployment.list_deployment()`|
|Удаление (если имеется служба с именем развертывания)|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Документация по API**. Ознакомьтесь со [справочной документацией](https://aka.ms/aml-packages/vision) с подробным описанием каждого модуля и класса.

**Справочник по CLI**. Более сложные операции, связанные с развертыванием, см. в [справочнике по CLI управления моделью](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Управление развертыванием на портале Azure**. Вы можете отслеживать и управлять развертываниями на [портале Azure](https://ms.portal.azure.com/). На портале Azure найдите страницу учетной записи Управления моделями Машинного обучения с помощью ее имени. Затем перейдите на страницу учетной записи Управления моделями и выберите "Управление моделями > Службы".


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Использование веб-службы 

После развертывания модели в качестве веб-службы вы можете оценивать изображения с ее помощью одним из следующих способов:

- Выполните оценку с помощью веб-службы непосредственно в объекте развертывания, используя `deploy_obj.score_image(image_path_or_url)`.

- Используйте URL-адрес конечной точки службы и ключ службы (отсутствует для локального развертывания) с такой строкой: `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Формируйте свои HTTP-запросы напрямую, чтобы получить оценку конечной точки веб-службы. Этот метод предназначен для опытных пользователей.

### <a name="score-with-existing-deployment-object"></a>Оценка с помощью имеющегося объекта развертывания

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Оценка с помощью URL-адреса конечной точки и ключа службы

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Оценка конечной точки с использованием HTTP-запроса непосредственно

В следующем примере кода формируется HTTP-запрос непосредственно в Python. Однако это можно сделать на других языках программирования.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Синтаксический анализ сериализованных данных из веб-службы

Выходные данные веб-службы представляют собой строку JSON. Вы можете проанализировать эту строку JSON с помощью моделей DNN различных классов.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о пакете Машинного обучения Azure для компьютерного зрения в этих статьях:

+ Узнайте, как [повысить точность модели](how-to-improve-accuracy-for-computer-vision-models.md).

+ Ознакомьтесь с [обзором пакета и узнайте, как его установить](https://aka.ms/aml-packages/vision).

+ Изучите [справочную документацию](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) для этого пакета.

+ Узнайте о [других пакетах Python для Машинного обучения Azure](reference-python-package-overview.md).
