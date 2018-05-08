---
title: Настройка образа контейнера для развертывания моделей машинного обучения Azure | Документация Майкрософт
description: В этой статье описывается, как настроить образ контейнера для моделей машинного обучения Azure
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 8ff2bca405bbd8faeaa4527f493804950fced6ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Настройка образа контейнера для моделей машинного обучения Azure

В этой статье описывается, как настроить образ контейнера для моделей машинного обучения Azure.  В Azure ML Workbench контейнеры используются для развертывания моделей машинного обучения. Модели развертываются вместе с зависимостями, и Azure ML формирует образ на основе модели, зависимостей и связанных файлов.

## <a name="how-to-customize-the-docker-image"></a>Настройка образа Docker
Настроить образ Docker, развертываемый Azure ML можно с использованием следующих средств:

1. Файл `dependencies.yml`: чтобы управлять зависимостями, устанавливаемыми из [PyPi]( https://pypi.python.org/pypi), можно использовать файл `conda_dependencies.yml` из проекта Workbench или создать собственный. Этот подход рекомендуется для установки зависимостей Python, которые устанавливаются с помощью PIP.

   Пример команды интерфейса командной строки:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Пример файла conda_dependencies: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. Файл действий Docker: при использовании этого параметра настраивается развертываемый образ при установке зависимостей, которые невозможно установить с помощью PyPi. 

   Файл должен содержать действия по установке Docker, например DockerFile. В этом файле разрешены следующие команды: 

    RUN, ENV, ARG, LABEL, EXPOSE.

   Пример команды интерфейса командной строки:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Команды Image, Manifest и Service принимают флаг docker-file.

   Пример файла действий Docker:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Базовый образ для контейнеров Azure ML включает Ubuntu. Он может быть изменен. Если указать другой базовый образ, он будет проигнорирован.

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы настроили образ контейнера, его можно развернуть в кластер для крупномасштабного использования.  Дополнительные сведения о настройке кластера для развертывания веб-службы см. в статье [Установка службы управления моделями](deployment-setup-configuration.md). 
