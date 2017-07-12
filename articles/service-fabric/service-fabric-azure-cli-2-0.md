---
title: "Начало работы с Service Fabric и Azure CLI 2.0"
description: "Использование командного модуля Service Fabric в Azure CLI версии 2.0, например, для подключения к кластеру и управления приложениями"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

# Service Fabric и Azure CLI 2.0

Новая версия Azure CLI 2.0 содержит команды для управления кластерами Service Fabric. В этой документации представлены инструкции по началу работы с Azure CLI.

<a id="install-azure-cli-20" class="xliff"></a>

## Установка Azure CLI 2.0

Теперь Azure CLI содержит команды для взаимодействия с кластерами Service Fabric и управления ими. Выполните [стандартный процесс установки](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), чтобы получить последнюю версию Azure CLI.

Дополнительные сведения см. в [документации по Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview)

<a id="cli-syntax" class="xliff"></a>

## Синтаксис CLI

Все команды Azure Service Fabric в Azure CLI начинаются с префикса `az sf`. Дополнительные сведения о доступных командах можно просмотреть, выполнив `az sf -h`. А выполнив `az sf <command> -h`, можно получить подробную справку по определенной команде.

Команды Azure Service Fabric в CLI соответствуют следующему шаблону именования:

```azurecli
az sf <object> <action>
```

Здесь `<object>` является целевым объектом для `<action>`.

<a id="selecting-a-cluster" class="xliff"></a>

## Выбор кластера

Перед выполнением любых операций выберите кластер, к которому нужно подключиться. Например, см. следующий фрагмент кода для подключения к незащищенному кластеру.

> [!WARNING]
> Не используйте незащищенные кластеры Service Fabric для рабочей среды.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

Конечная точка кластера должна начинаться с префикса `http` или `https` и включать номер порта для шлюза HTTP. Этот порт и адрес аналогичны URL-адресу Service Fabric Explorer.

Для кластеров, защищенных с помощью сертификата, поддерживаются незашифрованные файлы `pem` или `crt` и `key`.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Дополнительные сведения см. в [полной документации по подключению к защищенным кластерам](service-fabric-connect-to-secure-cluster.md).

> [!NOTE]
> При использовании команды select запросы не выполняются до получения результатов. Чтобы удостовериться, что кластер указан правильно, введите команду, например `az sf cluster health`, и проверьте, сопровождается ли выполнение ошибками.

<a id="performing-basic-operations" class="xliff"></a>

## Выполнение основных операций

Сведения о подключении кластера сохраняется для различных сеансов Azure CLI. Выбрав кластер Service Fabric, можно выполнить любую команду Service Fabric.

Например, чтобы получить сведения о работоспособности кластера Service Fabric, выполните следующую команду:

```azurecli
az sf cluster health
```

При условии, что в конфигурации Azure CLI указаны выходные данные JSON, будет получен следующий результат:

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

<a id="tips-and-faq" class="xliff"></a>

## Советы и часто задаваемые вопросы

Сведения ниже могут пригодиться при возникновении проблем с использованием команд Service Fabric в Azure CLI

<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

### Преобразование сертификата PFX в PEM

Azure CLI поддерживает клиентские сертификаты, такие как файлы PEM (расширение `.pem`). При использовании PFX-файлов из Windows эти сертификаты необходимо преобразовать в формат PEM. Чтобы преобразовать PFX-файл в PEM-файл, используйте следующую команду:

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Дополнительные сведения см. в [документации по OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html).

<a id="connection-issues" class="xliff"></a>

### Проблемы с подключением

Во время выполнения операций может возникать следующая ошибка:

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

В таком случае удостоверьтесь, что указанная конечная точка кластера доступна и ожидает передачи данных. Также проверьте, доступен ли для этого узла и порта пользовательский интерфейс Service Fabric Explorer. Обновите конечную точку с помощью `az sf cluster select`.

<a id="getting-detailed-logs" class="xliff"></a>

### Получение подробных журналов

При отладке или описании проблемы рекомендуется включать подробные журналы. В Azure CLI предусмотрен глобальный флаг `--debug`, повышающий уровень детализации журналов.

<a id="command-help-and-syntax" class="xliff"></a>

### Справка и синтаксис для команд

Команды Service Fabric соответствуют тому же соглашению, что и Azure CLI. Установите флаг `-h`, чтобы получить справку по определенной команде или группе команд. Например:

```azurecli
az sf application -h
```

или

```azurecli
az sf application create -h
```

