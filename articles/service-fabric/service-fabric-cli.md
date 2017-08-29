---
title: "Начало работы с интерфейсом командной строки Azure Service Fabric (sfctl)"
description: "Сведения об использовании интерфейса командной строки Azure Service Fabric. Узнайте, как подключится к кластеру и как управлять приложениями."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Командная строка Azure Service Fabric

Интерфейс командной строки Azure Service Fabric (sfctl) — служебная программа командной строки для взаимодействия с сущностями Azure Service Fabric и управления ими. Sfctl можно использовать с кластерами Windows и Linux. Sfctl выполняется на любой платформе, поддерживающей Python.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать установку убедитесь, что в вашей среде установлены Python и PIP. Дополнительные сведения см. в [кратком руководстве по началу работы с PIP](https://pip.pypa.io/en/latest/quickstart/) и официальной [документации по установке Python](https://wiki.python.org/moin/BeginnersGuide/Download).

Хотя поддерживается Python версий 2.7 и 3.6, рекомендуется использовать Python 3.6.

## <a name="install"></a>Установить

Интерфейс командной строки Azure Service Fabric представлен в виде пакета Python. Чтобы установить последнюю версию, выполните следующую команду:

```bash
pip install sfctl
```

После установки выполните команду `sfctl -h`, чтобы получить сведения о доступных командах.

## <a name="cli-syntax"></a>Синтаксис CLI

Команды всегда начинаются с префикса `sfctl`. Чтобы получить общие сведения о всех командах, которые можно применять, используйте `sfctl -h`. Для получения сведений об одной команде используйте `sfctl <command> -h`.

Команды подчиняются повторяющейся структуре, где целевой объект команды стоит перед командой (глаголом) или действием.

```azurecli
sfctl <object> <action>
```

В этом примере `<object>` — целевой объект для `<action>`.

## <a name="select-a-cluster"></a>Выбор кластера

Перед выполнением любых операций выберите кластер, к которому нужно подключиться. Например, выполните следующую команду, чтобы выбрать кластер `testcluster.com` и подключиться к нему.

> [!WARNING]
> Не используйте незащищенные кластеры Service Fabric в рабочей среде.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

Конечная точка кластера должна иметь префикс `http` или `https`. Она должна включать порт для шлюза HTTP. Этот порт и адрес аналогичны URL-адресу Service Fabric Explorer.

Для кластеров, защищенных с помощью сертификата, можно указать сертификат в кодировке PEM. Сертификат можно указать как один файл или как пару "сертификат — ключ".

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Дополнительные сведения см. в статье [Безопасное подключение к кластеру](service-fabric-connect-to-secure-cluster.md).

## <a name="basic-operations"></a>Базовые операции

Сведения о подключении кластера сохраняются между разными сеансами sfctl. Выбрав кластер Service Fabric, можно выполнить любую команду Service Fabric в кластере.

Например, чтобы получить сведения о работоспособности кластера Service Fabric, используйте следующую команду:

```azurecli
sfctl cluster health
```

Результаты команды должны быть следующими:

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

Некоторые рекомендации и советы по устранению распространенных проблем.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Преобразование сертификата PFX в формат PEM

Интерфейс командной строки Service Fabric поддерживает клиентские сертификаты, такие как PEM-файлы. При использовании PFX-файлов из Windows эти сертификаты необходимо преобразовать в формат PEM. Чтобы преобразовать PFX-файл в PEM-файл, используйте следующую команду:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Дополнительные сведения см. в [документации по OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-issues"></a>Проблемы с подключением

Некоторые операции могут создавать следующее сообщение:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Убедитесь, что указанная конечная точка кластера доступна и ожидает передачи данных. Также проверьте, доступен ли для этого узла и порта пользовательский интерфейс Service Fabric Explorer. Обновите конечную точку с помощью `sfctl cluster select`.

### <a name="detailed-logs"></a>Подробные журналы

Подробные журналы часто полезны при отладке или при сообщении о проблеме. В интерфейсе командной строки Service Fabric предусмотрен глобальный флаг `--debug`, повышающий уровень детализации файлов журналов.

### <a name="command-help-and-syntax"></a>Справка и синтаксис для команд

Для получения справки по определенной команде или группе команд используйте флаг `-h`:

```azurecli
sfctl application -h
```

Другой пример:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Дальнейшие действия

* [Manage an Azure Service Fabric application by using Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md) (Управление приложением Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric)
* [Prepare your development environment on Linux](service-fabric-get-started-linux.md) (Подготовка среды разработки в Linux)

