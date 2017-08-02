---
title: "Управление приложениями Azure Service Fabric с помощью Azure CLI 2.0"
description: "Узнайте, как развертывать приложения и удалить их из кластера с Azure Service Fabric с помощью Azure CLI 2.0."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 5728339236e3819b301e428f9d7a8add08f02b3e
ms.contentlocale: ru-ru
ms.lasthandoff: 07/14/2017

---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-cli-20"></a>Управление приложением Azure Service Fabric с помощью Azure CLI 2.0

Узнайте, как создавать и удалять приложения, выполняющиеся в кластере Azure Service Fabric.

## <a name="prerequisites"></a>Предварительные требования

* Установите Azure CLI 2.0 и выберите кластер Service Fabric. Дополнительные сведения можно найти в документации по [началу работы с Azure CLI 2.0](service-fabric-azure-cli-2-0.md).

* Также требуется готовый к развертыванию пакет приложения Service Fabric. Дополнительные сведения о том, как создавать и упаковывать приложения, представлены в [документации по модели приложения Service Fabric](service-fabric-application-model.md).

## <a name="overview"></a>Обзор

Чтобы развернуть новое приложение, выполните указанные ниже действия.

1. Отправьте пакет приложения в хранилище образов Service Fabric.
2. Подготовьте тип приложения.
3. Укажите приложение и создайте его.
4. Укажите службы и создайте их.

Чтобы удалить существующее приложение, выполните следующие действия.

1. Удалите приложение.
2. Отмените подготовку связанного типа приложения.
3. Удалите содержимое из хранилища образов.

## <a name="deploy-a-new-application"></a>Развертывание нового приложения

Чтобы развернуть новое приложение, выполните указанные ниже действия.

### <a name="upload-a-new-application-package-to-the-image-store"></a>Передача нового пакета приложения в хранилище образов

Прежде чем создавать приложение, необходимо отправить пакет приложения в хранилище образов Service Fabric. 

Предположим, что пакет приложения существует в каталоге `app_package_dir`. Чтобы отправить каталог, выполните следующие команды:

```azurecli
az sf application upload --path ~/app_package_dir
```

Для больших пакетов приложений вы можете указать параметр `--show-progress`, чтобы отобразить ход передачи.

### <a name="provision-the-application-type"></a>Подготовка типа приложения

По завершении отправки необходимо подготовить приложение. Чтобы подготовить приложение, выполните следующую команду:

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

Параметр `application-type-build-path` должен совпадать с именем каталога, содержащего пакет приложения, который был отправлен ранее.

### <a name="create-an-application-from-an-application-type"></a>Создание приложения из типа приложения

После подготовки приложения можно присвоить ему имя и создать приложение, выполнив следующую команду:

```azurecli
az sf application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name` — это имя, которое получит экземпляр приложения. Другие параметры можно найти с помощью манифеста приложения, который был подготовлен ранее.

Имя приложения должно начинаться с префикса `fabric:/`.

### <a name="create-services-for-the-new-application"></a>Создание служб для нового приложения

После создания приложения из него можно создавать службы. В этом примере мы создадим из приложения службу без отслеживания состояния. Службы, которые можно создать из приложения, определены в манифесте службы внутри подготовленного ранее пакета приложения.

```azurecli
az sf service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Проверка развертывания и работоспособности приложения

Чтобы проверить, что приложение и служба были успешно развернуты, убедитесь, что они отображаются:

```azurecli
az sf application list
az sf service list --application-list TestApp
```

Чтобы проверить, что служба работоспособна, выполните аналогичные команды, чтобы получить сведения о работоспособности службы и приложения.

```azurecli
az sf application health --application-id TestApp
az sf service health --service-id TestApp/TestSvc
```

Параметр `HealthState` работоспособных служб и приложений должен иметь значение `Ok`.

## <a name="remove-an-existing-application"></a>Удаление имеющегося приложения

Чтобы удалить приложение, выполните указанные ниже действия.

### <a name="delete-the-application"></a>Удаление приложения

Удалите приложение, выполнив следующую команду:

```azurecli
az sf application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>Отмена подготовки типа приложения

После удаления приложения можно также отменить подготовку типа приложения (если он больше не требуется). Для этого выполните следующую команду:

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

Имя и версия типа должны соответствовать имени и версии в манифесте приложения, который был подготовлен ранее.

### <a name="delete-the-application-package"></a>Удаление пакета приложения

После того как подготовка типа приложения была отменена, пакет приложения можно удалить из хранилища образов (если он больше не требуется). Удалите пакеты приложения, чтобы освободить место на диске. 

Чтобы удалить пакет приложения из хранилища образов, выполните следующую команду:

```azurecli
az sf application package-delete --content-path app_package_dir
```

Параметр `content-path` должен совпадать с именем каталога, загруженного при создании приложения.

## <a name="related-articles"></a>Связанные статьи

* [Service Fabric и Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)

