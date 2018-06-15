---
title: Пакет FPGA для аппаратного ускорения службы "Машинное обучение Azure"
description: Сведения о пакетах Python, доступных для пользователей службы "Машинное обучение Azure".
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: routlaw
author: rloutlaw
ms.date: 05/07/2018
ms.openlocfilehash: e680ef34be1d5dae2942c432de5e81fe620bbdc4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832984"
---
# <a name="azure-machine-learning-hardware-acceleration-package"></a>Пакет аппаратного ускорения для службы "Машинное обучение Azure"

Пакет для аппаратного ускорения службы "Машинное обучение Azure" — это расширение Python, установленное с помощью PIP, для службы "Машинное обучение Azure", которое позволяет специалистам по обработке и анализу данных и разработчикам систем искусственного интеллекта быстро выполнять следующие действия:

+ создание признаков изображений с помощью квантованной версии ResNet 50;

+ обучение классификатора на основе этих признаков;

+ развертывание моделей в [программируемые пользователем вентильные матрицы (FPGA)](concept-accelerate-with-fpgas.md) в Azure для обеспечения сверхнизкой задержки.

## <a name="prerequisites"></a>предварительным требованиям

1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

1. Вы должны создать учетную запись Управления моделями Машинного обучения Azure. Дополнительные сведения о создании учетной записи см. в документе [Краткое руководство. Установка и начало работы со службами Машинного обучения Azure](../service/quickstart-installation.md). 

1. Необходимо установить пакет. 

 
## <a name="how-to-install-the-package"></a>Установка пакета

1. Скачайте и установите последнюю версию [Git](https://git-scm.com/downloads).

2. Установите [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Чтобы скачать предварительно настроенную среду Anaconda, выполните следующую команду из командной строки Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```
5. Чтобы создать среду, откройте **командную строку Anaconda** и выполните следующую команду:

    ```
    conda env create -f aml-real-time-ai/environment.yml
    ```

6. Включите среду, выполнив следующую команду:

    ```
    conda activate amlrealtimeai
    ```

## <a name="sample-code"></a>Пример кода

В этом примере кода описывается использование пакета SDK для развертывания модели в FPGA.

1. Импортируйте пакет:
   ```python
   import amlrealtimeai
   from amlrealtimeai import resnet50
   ```

1. Предварительно обработайте изображение:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Создайте признаки изображений:
   ```python 
   from amlrealtimeai.resnet50.model import LocalQuantizedResNet50
   model_path = os.path.expanduser('~/models')
   model = LocalQuantizedResNet50(model_path)
   print(model.version)
   ```

1. Создайте классификатор:
   ```python
   model.import_graph_def(include_featurizer=False)
   print(model.classifier_input)
   print(model.classifier_output)
   ```

1. Создайте определение службы:
   ```python
   from amlrealtimeai.pipeline import ServiceDefinition, TensorflowStage, BrainWaveStage
   save_path = os.path.expanduser('~/models/save')
   service_def_path = os.path.join(save_path, 'service_def.zip')

   service_def = ServiceDefinition()
   service_def.pipeline.append(TensorflowStage(tf.Session(), in_images, image_tensors))
   service_def.pipeline.append(BrainWaveStage(model))
   service_def.pipeline.append(TensorflowStage(tf.Session(), model.classifier_input, model.classifier_output))
   service_def.save(service_def_path)
   print(service_def_path)
   ```
 
1. Подготовьте модель для запуска в FPGA:
   ```python
   from amlrealtimeai import DeploymentClient

   subscription_id = "<Your Azure Subscription ID>"
   resource_group = "<Your Azure Resource Group Name>"
   model_management_account = "<Your AzureML Model Management Account Name>"

   model_name = "resnet50-model"
   service_name = "quickstart-service"

   deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account)
   ```

1. Разверните модель для запуска в FPGA:
   ```python
   service = deployment_client.get_service_by_name(service_name)
   model_id = deployment_client.register_model(model_name, service_def_path)

   if(service is None):
      service = deployment_client.create_service(service_name, model_id)    
   else:
      service = deployment_client.update_service(service.id, model_id)
   ```

1. Создайте клиент:
    ```python
   from amlrealtimeai import PredictionClient
   client = PredictionClient(service.ipAddress, service.port)  
   ```

1. Вызовите API:
   ```python
   image_file = R'C:\path_to_file\image.jpg'
   results = client.score_image(image_file)
   ```

## <a name="reporting-issues"></a>Создание отчетов о проблемах

Вы можете сообщить о проблемах, которые возникли с пакетом, на [форуме](https://aka.ms/aml-forum).

## <a name="next-steps"></a>Дополнительная информация

[Развертывание модели как веб-службы в FPGA с помощью службы "Машинное обучение Azure"](how-to-deploy-fpga-web-service.md)