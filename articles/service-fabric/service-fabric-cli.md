---
title: "Начало работы с интерфейсом командной строки Azure Service Fabric"
description: "Сведения об использовании интерфейса командной строки Azure Service Fabric. Узнайте, как подключиться к кластеру и как управлять приложениями."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 851f04c8b5eee762ec43060f02c8b83f00c1782e
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="azure-service-fabric-cli"></a>Интерфейс командной строки Azure Service Fabric

Интерфейс командной строки Azure Service Fabric — это служебная программа командной строки для взаимодействия с сущностями Service Fabric и управления ими. Интерфейс командной строки Service Fabric можно использовать с кластерами Windows и Linux. Он выполняется на любой платформе, поддерживающей Python.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать установку, убедитесь, что в вашей среде установлены Python и pip. Дополнительные сведения см. в [кратком руководстве по началу работы с pip](https://pip.pypa.io/en/latest/quickstart/) и официальной [документации по установке Python](https://wiki.python.org/moin/BeginnersGuide/Download).

Хотя поддерживается Python версий 2.7 и 3.6, мы рекомендуем использовать Python 3.6. В следующем разделе показано, как установить все необходимые компоненты и интерфейс командной строки.

## <a name="install-pip-python-and-the-service-fabric-cli"></a>Установка pip, Python и интерфейса командной строки Service Fabric

 Существует много способов установки pip и Python на вашей платформе. Ниже приведены действия для быстрой установки Python 3.6 и pip, которые подходят для большинства операционных систем.

### <a name="windows"></a>Windows

Для Windows 10, Windows Server 2016 и Windows Server 2012 R2 используйте стандартные официальные инструкции по установке. Установщик Python также по умолчанию устанавливает pip.

1. Перейдите на официальную [страницу загрузок Python](https://www.python.org/downloads/) и скачайте последний выпуск Python 3.6.

2. Запустите установщик.

3. В нижней части окна запроса выберите **Add Python 3.6 to PATH** (Добавить Python 3.6 в PATH).

4. Выберите **Install Now** (Установить сейчас) и завершите установку.

Теперь можно открыть новое командное окно, чтобы узнать версию Python и pip.

```bat
python --version
pip --version
```

Затем выполните следующую команду, чтобы установить интерфейс командной строки Service Fabric.

```
pip install sfctl
sfctl -h
```

### <a name="ubuntu"></a>Ubuntu

В Ubuntu 16.04 Desktop вы можете установить Python 3.6, используя персональные архивы пакетов сторонних разработчиков (PPA).

В окне терминала выполните следующие команды:

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

Затем, чтобы установить интерфейс командной строки Service Fabric для только что установленной версии Python 3.6, выполните следующую команду:

```bash
python3.6 -m pip install sfctl
sfctl -h
```

Эти действия не повлияют на системную установку Python 3.5 или 2.7. Не пытайтесь изменить эти установленные версии, если вы не знакомы с Ubuntu.

### <a name="macos"></a>MacOS

Для MacOS мы рекомендуем использовать [диспетчер пакетов HomeBrew](https://brew.sh). Если диспетчер HomeBrew еще не установлен, установите его, выполнив следующую команду:

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Затем с помощью терминала установите Python 3.6, pip и интерфейс командной строки Service Fabric, выполнив следующие команды:

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

Эти действия не повлияют на системную установку Python 2.7.

## <a name="cli-syntax"></a>Синтаксис CLI

Команды всегда начинаются с префикса `sfctl`. Чтобы получить общие сведения обо всех командах, которые можно применять, используйте `sfctl -h`. Для получения сведений об одной команде используйте `sfctl <command> -h`.

Команды подчиняются повторяющейся структуре, где целевой объект команды указан перед командой (глаголом) или действием.

```azurecli
sfctl <object> <action>
```

В этом примере `<object>` — целевой объект для `<action>`.

## <a name="select-a-cluster"></a>Выбор кластера

Перед выполнением любых операций выберите кластер, к которому нужно подключиться. Например, чтобы выбрать кластер `testcluster.com` и подключиться к нему, выполните следующую команду:

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

Сведения о подключении кластера сохраняются между различными сеансами интерфейса командной строки Service Fabric. Выбрав кластер Service Fabric, можно выполнить любую команду Service Fabric в кластере.

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

В этом разделе приведены рекомендации и советы по устранению распространенных проблем.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>Преобразование сертификата PFX в формат PEM

Интерфейс командной строки Service Fabric поддерживает клиентские сертификаты, такие как PEM-файлы. При использовании PFX-файлов из Windows эти сертификаты необходимо преобразовать в формат PEM. Чтобы преобразовать PFX-файл в PEM-файл, используйте следующую команду:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Дополнительные сведения см. в [документации по OpenSSL](https://www.openssl.org/docs/).

### <a name="connection-problems"></a>Проблемы с подключением

Некоторые операции могут создавать следующее сообщение:

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

Убедитесь, что указанная конечная точка кластера доступна и ожидает передачи данных. Также проверьте, доступен ли для этого узла и порта пользовательский интерфейс Service Fabric Explorer. Обновите конечную точку с помощью `sfctl cluster select`.

### <a name="detailed-logs"></a>Подробные журналы

Подробные журналы часто полезны при отладке или при сообщении о проблеме. Глобальный флаг `--debug` повышает уровень детализации файлов журналов.

### <a name="command-help-and-syntax"></a>Справка и синтаксис для команд

Для получения справки по определенной команде или группе команд используйте флаг `-h`.

```azurecli
sfctl application -h
```

Вот еще один пример:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>Дальнейшие действия

* [Manage an Azure Service Fabric application by using Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md) (Управление приложением Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric)
* [Prepare your development environment on Linux](service-fabric-get-started-linux.md) (Подготовка среды разработки в Linux)

