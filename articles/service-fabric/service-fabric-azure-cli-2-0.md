---
title: "Начало работы с Azure Service Fabric и Azure CLI 2.0"
description: "Узнайте, как использовать команды модуля Service Fabric в Azure CLI версии 2.0 Узнайте, как подключится к кластеру и как управлять приложениями."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: ru-ru
ms.lasthandoff: 07/14/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric и Azure CLI 2.0

Интерфейс командной строки Azure (Azure CLI) версии 2.0 включает команды для управления кластерами Azure Service Fabric. Узнайте, как начать работу с Azure CLI и Service Fabric.

## <a name="install-azure-cli-20"></a>Установка Azure CLI 2.0

Команды Azure CLI 2.0 можно использовать для взаимодействия с кластерами Service Fabric и управления ими. Чтобы получить последнюю версию Azure CLI, следуйте [стандартному процессу установки Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

Дополнительные сведения см. в [обзоре Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview).

## <a name="azure-cli-syntax"></a>Синтаксис Azure CLI

Все команды Service Fabric в Azure CLI начинаются с префикса `az sf`. Чтобы получить общие сведения о командах, которые можно применять, используйте `az sf -h`. Для получения сведений об одной команде используйте `az sf <command> -h`.

Команды Service Fabric в Azure CLI соответствуют такому шаблону именования:

```azurecli
az sf <object> <action>
```

Здесь `<object>` является целевым объектом для `<action>`.

## <a name="select-a-cluster"></a>Выбор кластера

Перед выполнением любых операций выберите кластер, к которому нужно подключиться. В качестве примера ниже приведен код. Код подключается к незащищенному кластеру.

> [!WARNING]
> Не используйте незащищенные кластеры Service Fabric в рабочей среде.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

Конечная точка кластера должна иметь префикс `http` или `https`. Она должна включать порт для шлюза HTTP. Этот порт и адрес аналогичны URL-адресу Service Fabric Explorer.

Для кластеров, защищенных с помощью сертификата, можно использовать не зашифрованные PEM-файлы или CRT-файлы и KEY-файлы. Например:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Дополнительные сведения см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> Команда `select` не обрабатывает запросы до возвращения. Чтобы убедиться, что вы указали кластер правильно, используйте команды, аналогичные `az sf cluster health`. Убедитесь, что команда не возвращает каких-либо ошибок.

## <a name="basic-operations"></a>Базовые операции

Сведения о подключении кластера сохраняется между различными сеансами Azure CLI. Выбрав кластер Service Fabric, можно выполнить любую команду Service Fabric в кластере.

Например, чтобы получить сведения о работоспособности кластера Service Fabric, используйте следующую команду:

```azurecli
az sf cluster health
```

При условии, что в конфигурации Azure CLI указаны выходные данные JSON, будет получен следующий результат:

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>Рекомендации и устранение неполадок

Следующие сведения могут оказаться полезными в случае возникновения проблем при использовании команд Service Fabric в Azure CLI.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Преобразование сертификата PFX в формат PEM

Azure CLI поддерживает клиентские сертификаты, такие как PEM-файлы. При использовании PFX-файлов из Windows эти сертификаты необходимо преобразовать в формат PEM. Чтобы преобразовать PFX-файл в PEM-файл, используйте следующую команду:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Дополнительные сведения см. в [документации по OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Проблемы с подключением

Некоторые операции могут создавать следующее сообщение:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Убедитесь, что указанная конечная точка кластера доступна и ожидает передачи данных. Также проверьте, доступен ли для этого узла и порта пользовательский интерфейс Service Fabric Explorer. Обновите конечную точку с помощью `az sf cluster select`.

### <a name="detailed-logs"></a>Подробные журналы

Подробные журналы часто полезны при отладке или при сообщении о проблеме. В Azure CLI предусмотрен глобальный флаг `--debug`, повышающий уровень детализации файлов журналов.

### <a name="command-help-and-syntax"></a>Справка и синтаксис для команд

Команды Service Fabric соответствуют тому же соглашению, что и Azure CLI. Для получения справки по определенной команде или группе команд используйте флаг `-h`:

```azurecli
az sf application -h
```

Вот еще один пример:

```azurecli
az sf application create -h
```

