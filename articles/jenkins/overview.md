---
title: Общие сведения о Jenkins и Azure | Документация Майкрософт
description: Размещение сборки Jenkins и развертывание сервера автоматизации в Azure, а также использование вычислительных ресурсов и ресурсов службы хранилища Azure для расширения конвейеров непрерывной интеграции и непрерывного развертывания (CI/CD).
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: ca4a6cb886b0453848dc7b29e15de2063878a65d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30832679"
---
# <a name="azure-and-jenkins"></a>Azure и Jenkins

[Jenkins](https://jenkins.io/) — это популярный сервер автоматизации с открытым кодом, используемый для внедрения непрерывной интеграции и непрерывной доставки (CI/CD) для проектов программного обеспечения. Вы можете разместить развертывание Jenkins в Azure либо расширить имеющуюся конфигурацию Jenkins с помощью ресурсов Azure. Кроме того, можно использовать подключаемые модули Jenkins, чтобы упростить непрерывную интеграцию и непрерывную доставку приложений в Azure.

Это вводная статья об использовании Azure с Jenkins, в которой подробно рассматриваются основные возможности Azure, доступные пользователям Jenkins. Чтобы приступить к работе с сервером Jenkins в Azure, изучите наше [краткое руководство](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Размещение серверов Jenkins в Azure

Разместите Jenkins в Azure, чтобы централизовать автоматизацию выполнения сборки и масштабировать развертывание по мере роста потребностей проектов программного обеспечения. Jenkins можно развернуть в Azure с помощью:
 
- [шаблона решения Jenkins](install-jenkins-solution-template.md) из Azure Marketplace;
- [виртуальных машин Azure](/azure/virtual-machines/linux/overview). Создание экземпляра Jenkins на виртуальной машине описывается в нашем [руководстве](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd).
- Если используется кластер Kubernetes в [Службе контейнеров Azure](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), ознакомьтесь с нашим [практическим руководством](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Для мониторинга развертывания Jenkins в Azure и управления им можно использовать [Log Analytics](/azure/log-analytics/log-analytics-overview) и [Azure CLI](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Масштабирование по требованию автоматизации выполнения сборки

Вы можете добавлять агенты сборки в существующее развертывание Jenkins, чтобы масштабировать производительность выполнения сборки Jenkins по мере увеличения числа сборок и усложнения заданий и конвейеров. Эти агенты сборки можно запускать на виртуальных машинах Azure с помощью [подключаемого модуля агентов виртуальных машин](jenkins-azure-vm-agents.md). Ознакомьтесь с нашим [руководством](/azure/jenkins/jenkins-azure-vm-agents), чтобы получить дополнительные сведения.

После настройки [субъекта-службы Azure](/azure/azure-resource-manager/resource-group-overview) для заданий и конвейеров Jenkins эти учетные данные можно будет использовать для следующего:

- Для безопасного хранения и архивации артефактов сборки в [службе хранилища Azure](/azure/storage/common/storage-introduction) с помощью [подключаемого модуля службы хранилища Azure](https://plugins.jenkins.io/windows-azure-storage). Просмотрите [практическое руководство по хранилищу Jenkins](/azure/storage/common/storage-java-jenkins-continuous-integration-solution), чтобы получить дополнительные сведения.
- Для управления и настройки ресурсов Azure с помощью [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Развертывание кода в службах Azure

Используйте подключаемые модули Jenkins для развертывания приложений в Azure в рамках конвейеров непрерывной интеграции и непрерывной доставки Jenkins. Развертывание в [службе приложений Azure](/azure/app-service/) и [Службе контейнеров Azure](/azure/container-service/kubernetes/) позволяет поэтапно создавать и тестировать приложения, а также выпускать обновления для них без необходимости управлять базовой инфраструктурой.

 Доступны подключаемые модули для развертывания в следующих службах и средах.

- [Веб-приложение Azure в Linux](/azure/app-service/containers/app-service-linux-intro). Чтобы приступить к работе, ознакомьтесь с этим [руководством](java-deploy-webapp-tutorial.md).
- [Веб-приложение Azure](/azure/app-service/app-service-web-overview). Чтобы приступить к работе, ознакомьтесь с этим [практическим руководством](deploy-Jenkins-app-service-plugin.md).

