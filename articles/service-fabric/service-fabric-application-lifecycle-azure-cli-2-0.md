---
title: "Управление приложениями Service Fabric с помощью Azure CLI 2.0"
description: "Здесь описывается процесс развертывания и удаления приложений из кластера Service Fabric с помощью Azure CLI 2.0."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: a99819f6a1c0ef31e14c95b6bd47138feb05053f
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
# <a name="manage-service-fabric-application-using-azure-cli-20"></a>Управление приложением Service Fabric с помощью Azure CLI 2.0

Следуйте инструкциям в этой документации, чтобы научиться создавать и удалять приложения, выполняющиеся в кластере Service Fabric.

## <a name="prerequisites"></a>Предварительные требования

Не забудьте установить Azure CLI 2.0 и выбрать кластер Service Fabric. Дополнительные сведения можно найти в документации по [началу работы с Azure CLI 2.0](service-fabric-azure-cli-2-0.md).

Вам также необходимо иметь готовый к развертыванию пакет приложения Service Fabric. Дополнительные сведения о том, как создавать и упаковывать приложения, можно найти в [документации по модели приложения](service-fabric-application-model.md).

## <a name="overview"></a>Обзор

Развертывание нового приложения состоит из четырех шагов:

1. Отправка пакета приложения в хранилище образов Service Fabric.
1. Подготовка типа приложения.
1. Указание и создание приложения.
1. Указание и создание служб.

Удаление имеющегося приложения состоит из трех шагов:

1. Удаление приложения
1. Отмена подготовки связанного типа приложения.
1. Удаление содержимого хранилища образов.

## <a name="deploy-a-new-application"></a>Развертывание нового приложения

Чтобы развернуть новое приложение, выполните указанные ниже действия.

### <a name="upload-a-new-application-package-to-the-image-store"></a>Передача нового пакета приложения в хранилище образов

Прежде чем создавать приложение, необходимо передать пакет приложения в хранилище образов Service Fabric.
Предположим, что пакет приложения существует в каталоге `app_package_dir`. Чтобы передать каталог, выполните следующие команды:

```azurecli
az sf application upload --path ~/app_package_dir
```

Для больших пакетов приложений вы можете указать параметр `--show-progress`, чтобы отобразить ход передачи.

### <a name="provision-application-type"></a>Подготовка типа приложения

После завершения передачи необходимо подготовить приложение. Чтобы подготовить приложение, выполните следующую команду:

```azurecli
az sf application provision --application-type-build-path app_package_dir
```

`application-type-build-path` должен совпадать с именем каталога, содержащего пакет приложения, который был передан ранее.

### <a name="create-application-from-application-type"></a>Создание приложения из типа приложения

После подготовки приложения вы можете назвать и создать приложение, выполнив следующую команду:

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

## <a name="verify-application-creation-and-health"></a>Проверка создания и работоспособности приложения

Чтобы проверить, что приложение и служба были успешно развернуты, убедитесь, что они отображаются, выполнив следующие команды:

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

После удаления приложения можно также отменить подготовку типа приложения (если он не больше требуется). Чтобы отменить подготовку типа приложения, выполните следующую команду:

```azurecli
az sf application unprovision --application-type-name TestAppTye --application-type-version 1.0
```

Имя и версия типа должны соответствовать имени и версии в манифесте приложения, который был подготовлен ранее.

### <a name="delete-application-package"></a>Удаление пакета приложения

После того как подготовка типа приложения была отменена, пакет приложения можно удалить из хранилища образов (если он больше не требуется). Удалите пакеты приложения, чтобы освободить место на диске. Чтобы удалить пакет приложения из хранилища образов, выполните следующую команду:

```azurecli
az sf application package-delete --content-path app_package_dir
```

`content-path` должен совпадать с именем каталога, загруженного при создании приложения.

## <a name="related-articles"></a>Связанные статьи

* [Service Fabric и Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)

