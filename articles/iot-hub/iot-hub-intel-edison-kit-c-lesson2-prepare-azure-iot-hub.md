---
title: "Подключение Intel Edison (C) к Интернету вещей Azure. Урок 2. Регистрация устройства | Документация Майкрософт"
description: "Создайте группу ресурсов и Центр Интернета вещей Azure, а также зарегистрируйте в нем устройство Edison с помощью Azure CLI."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 80bfc3cd-a1fc-4775-8994-d8033381dd3d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 9eebae32bf469e0744855ed9f98306bd36eb9671
ms.contentlocale: ru-ru
ms.lasthandoff: 01/25/2017

---
<a id="create-your-iot-hub-and-register-intel-edison" class="xliff"></a>

# Создание Центра Интернета вещей и регистрация Intel Edison
<a id="what-you-will-do" class="xliff"></a>

## Выполняемая задача
* Создайте группу ресурсов.
* Создайте Центр Интернета вещей Azure в группе ресурсов.
* Добавьте устройство Intel Edison в Центр Интернета вещей Azure с помощью интерфейса командной строки Azure (Azure CLI).

При использовании Azure CLI для добавления устройства Edison в Центр Интернета вещей служба создает ключ для устройства Edison, чтобы аутентифицировать его в службе. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## Новые знания
В этой статье вы узнаете следующее:
* Как создать Центр Интернета вещей с помощью Azure CLI.
* Как создать удостоверения устройства для устройства Edison в Центре Интернета вещей.

<a id="what-you-need" class="xliff"></a>

## Необходимые элементы
* Учетная запись Azure. Если ее нет, можно создать [бесплатную пробную учетную запись Azure](http://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.
* У вас должен быть установлен Azure CLI.

<a id="create-your-iot-hub" class="xliff"></a>

## Создание Центра Интернета вещей
Центр Интернета вещей Azure позволяет подключать и отслеживать миллионы ресурсов Интернета вещей и управлять ими. Выполните следующие действия, чтобы создать Центр Интернета вещей:

1. Войдите в свою учетную запись Azure с помощью следующей команды:

   ```bash
   az login
   ```

   После входа отобразится список всех доступных подписок Azure.

2. Укажите подписку, которую необходимо использовать по умолчанию, выполнив следующую команду.

   ```bash
   az account set --subscription {subscription id or name}
   ```

   Значение `subscription ID or name` можно найти в выходных данных команды `az login` или `az account list`.

3. Зарегистрируйте поставщик с помощью следующей команды. Поставщики ресурсов — это службы, предоставляющие ресурсы для приложения. Поставщик следует регистрировать перед развертыванием ресурса Azure, предлагаемого поставщиком.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. Создайте группу ресурсов с именем iot-sample в западном регионе США, выполнив следующую команду:

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` — это расположение, в котором создается группа ресурсов. Чтобы использовать другое расположение, выполните команду `az account list-locations -o table` и просмотрите все расположения, поддерживаемые Azure.

5. Создайте Центр Интернета вещей в группе ресурсов iot-sample, выполнив следующую команду:

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

По умолчанию создается Центр Интернета вещей уровня "Бесплатный". Дополнительные сведения см. на странице [Центр Интернета вещей Azure — Цены](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE] 
> Имя Центра Интернета вещей должно быть глобально уникальным.
> В подписке Azure можно создать только один выпуск Центра Интернета вещей категории F1.


<a id="register-edison-in-your-iot-hub" class="xliff"></a>

## Регистрация устройства Edison в Центре Интернета вещей
Каждое устройство, которое отправляет сообщения в ваш Центр Интернета вещей и получает сообщения из него, должно быть зарегистрировано с использованием уникального идентификатора.

Зарегистрируйте устройство Edison в Центре Интернета вещей, выполнив следующую команду:

```bash
az iot device create --device-id myinteledison --hub-name {my hub name}
```

<a id="summary" class="xliff"></a>

## Сводка
Вы создали Центр Интернета вещей и зарегистрировали в нем устройство Edison с идентификатором устройства. Теперь можно перейти к отправке сообщений с устройства Edison в Центр Интернета вещей.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
[Создание приложения-функции Azure и учетной записи хранения Azure][process-and-store-iot-hub-messages].


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
