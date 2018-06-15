---
title: Добавление кластера Kubernetes в Azure Stack Marketplace | Документы Майкрософт
description: Сведения о добавлении кластера Kubernetes в Azure Stack Marketplace.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 0ba0e1f3d9e0f1cbb6ba4109a21fc29dc41df5fc
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603459"
---
# <a name="add-a-kubernetes-cluster-to-the-azure-stack-marketplace"></a>Добавление кластера Kubernetes в Azure Stack Marketplace

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!note]  
> Kubernetes для Службы контейнеров Azure (ACS) в Azure Stack доступен в закрытой предварительной версии. Чтобы запросить доступ к элементу Kubernetes в Marketplace, необходимому для выполнения инструкций в этой статье, [отправьте запрос на получение доступа](https://aka.ms/azsk8).

Кластер Kubernetes можно предлагать пользователям в качестве элемента Marketplace. Развертывание Kubernetes выполняется за одну согласованную операцию.

В этой статье рассматривается развертывание и подготовка ресурсов для автономного кластера Kubernetes с помощью шаблона Azure Resource Manager. Прежде чем начать, проверьте настройки Azure Stack и глобальные параметры клиента Azure. Соберите необходимые сведения об Azure Stack. Добавьте необходимые ресурсы в клиент и Azure Stack Marketplace. Для добавления кластера в Marketplace требуются элементы сервера Ubuntu, настраиваемого скрипта и кластера Kubernetes.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Создание плана, предложения и подписки

Создайте план, предложение и подписку для элемента кластера Kubernetes в Marketplace. Можно также использовать имеющийся план и предложение.

1. Войдите на [портал администрирования.](https://adminportal.local.azurestack.external)

2. Создайте базовый план. Инструкции см. в статье [Создание плана в Azure Stack](azure-stack-create-plan.md).

3. Создайте предложение. Инструкции см. в статье [Создание предложения в Azure Stack](azure-stack-create-offer.md).

4. Выберите **Предложения** и найдите созданное предложение.

5. В колонке "Предложение" выберите **Обзор**.

6. Щелкните **Изменить состояние**. Щелкните **Общедоступный**.

7. Чтобы создать подписку, выберите **+ Создать** > **Предложения и планы** > **Подписки**.

    a. Введите значение в поле **Отображаемое имя**.

    Б. Введите значение в поле **Пользователь**. Используйте учетную запись Azure AD, связанную с вашим клиентом.

    c. **Описание поставщика**

    d. В поле **Клиент каталога** укажите клиент Azure AD для Azure Stack. 

    д. Выберите **Предложение**. Выберите имя созданного предложения. Запишите идентификатор подписки.

## <a name="add-an-ubuntu-server-image"></a>Добавление образа сервера Ubuntu

Добавьте в Marketplace следующий образ сервера Ubuntu:

1. Войдите на [портал администрирования](https://adminportal.local.azurestack.external).

2. Щелкните **Больше служб** > **Marketplace Management (Управление Marketplace)**.

3. Выберите **+ Add from Azure** (Добавить из Azure).

4. Укажите `UbuntuServer`.

5. Выберите сервер со следующим профилем:
    - **Издатель** : Canonical
    - **Предложение**: UbuntuServer
    - **SKU**: 16.04 LTS
    - **Версия**: 16.04.201802220

    > [!Note]  
    > Могут отображаться несколько версий Ubuntu Server 16.04 LTS. Необходимо добавить соответствующую версию. Для кластера Kubernetes требуется точная версия элемента.

6. Выберите **Скачать**.

## <a name="add-a-custom-script-for-linux"></a>Добавление настраиваемого скрипта для Linux

Добавьте кластер Kubernetes из Marketplace, выполнив следующие действия.

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

2. Щелкните **Больше служб** > **Marketplace Management (Управление Marketplace)**.

3. Выберите **+ Add from Azure** (Добавить из Azure).

4. Укажите `Custom Script for Linux`.

5. Выберите скрипт со следующим профилем:
    - **Предложение**: настраиваемый скрипт для Linux 2.0
    - **Версия**: 2.0.3
    - **Издатель**: корпорация Майкрософт

    > [!Note]  
    > Могут отображаться несколько версий настраиваемого скрипта для Linux. Необходимо добавить соответствующую версию. Для кластера Kubernetes требуется точная версия элемента.

6. Выберите **Скачать**.


## <a name="add-the-kubernetes-cluster-to-the-marketplace"></a>Добавление кластера Kubernetes в Marketplace

1. Откройте [портал администрирования](https://adminportal.local.azurestack.external).

2. Щелкните **Больше служб** > **Marketplace Management (Управление Marketplace)**.

3. Выберите **+ Add from Azure** (Добавить из Azure).

4. Укажите `Kubernetes Cluster`.

5. Выберите `Kubernetes Cluster`.

6. Выберите **Скачать**.

    > [!note]  
    > Для отображения элемента в Marketplace может потребоваться пять минут.

    ![Кластер Kubernetes](user\media\azure-stack-solution-template-kubernetes-deploy\marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes-cluster"></a>Обновление или удаление кластера Kubernetes 

При обновлении элемента кластера Kubernetes будет необходимо удалить элемент, находящийся в Marketplace. После этого можно выполнить приведенные в этой статье инструкции по добавлению кластера Kubernetes в Marketplace.

Чтобы удалить элемент кластера Kubernetes:

1. Запомните имя текущего элемента, например `Microsoft.AzureStackKubernetesCluster.0.1.0`.

2. Подключитесь к Azure Stack с помощью PowerShell.

3. Удалите элемент, выполнив следующий командлет PowerShell:

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.1.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Дополнительная информация

[Развертывание кластера Kubernetes в Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)



[Общие сведения о предложении служб в Azure Stack](azure-stack-offer-services-overview.md)