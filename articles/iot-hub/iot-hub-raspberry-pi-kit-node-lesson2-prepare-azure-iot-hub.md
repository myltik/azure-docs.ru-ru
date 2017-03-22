---
featureFlags:
- usabilla
title: "Подключение Raspberry Pi (Node) к Интернету вещей Azure. Урок 2. Регистрация устройства | Документация Майкрософт"
description: "Создание группы ресурсов и Центра Интернета вещей Azure, а также регистрация устройства Pi в реестре удостоверений Центра Интернета вещей с помощью Azure CLI."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "облако raspberry pi, облачное подключение pi"
ms.assetid: 736215b6-e7e4-46f9-af30-0ded9ffa5204
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: add684549056a824e5534d071a5d0215e27f7a0e
ms.lasthandoff: 01/24/2017


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Создание Центра Интернета вещей и регистрация Raspberry Pi 3
## <a name="what-you-will-do"></a>Выполняемая задача
* Создайте группу ресурсов.
* Создайте Центр Интернета вещей Azure в группе ресурсов.
* Добавьте устройство Raspberry Pi 3 в Центр Интернета вещей Azure с помощью интерфейса командной строки Azure (Azure CLI).

При использовании интерфейса командной строки Azure для добавления Pi в Центр Интернета вещей служба создает ключ для устройства Pi, что выполнить в службе его аутентификацию. Если возникнут какие-либо проблемы, то решения можно найти на [странице со сведениями об устранении неполадок](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Новые знания
В этой статье вы узнаете следующее:
* Как использовать интерфейс командной строки Azure для создания Центра Интернета вещей.
* Как создать удостоверение устройства для Pi в Центре Интернета вещей.

## <a name="what-you-need"></a>Необходимые элементы
* Учетная запись Azure.
* Компьютер под управлением Mac или Windows с установленным интерфейсом командной строки Azure.

## <a name="create-your-iot-hub"></a>Создание Центра Интернета вещей
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
> Имя Центра Интернета вещей должно быть глобально уникальным. В подписке Azure можно создать только один выпуск Центра Интернета вещей категории F1.

## <a name="register-pi-in-your-iot-hub"></a>Регистрация устройства Pi в Центре Интернета вещей
Каждое устройство, которое отправляет сообщения в ваш Центр Интернета вещей и получает сообщения из него, должно быть зарегистрировано с использованием уникального идентификатора. Регистрация устройства Pi и создание самозаверяющего сертификата X.509 для аутентификации устройства выполняется с помощью интерфейса командной строки Azure.

Выполните следующую команду:

```bash
# For Windows command prompt
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir %USERPROFILE%\.iot-hub-getting-started
 
# For macOS or Ubuntu
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir ~/.iot-hub-getting-started
```

## <a name="summary"></a>Сводка
Вы создали Центр Интернета вещей и зарегистрировали в этом центре устройство Pi с идентификатором устройства. Теперь можно перейти к отправке сообщений с устройства Pi в Центр Интернета вещей.

## <a name="next-steps"></a>Дальнейшие действия
[Create an Azure function app and an Azure Storage account to process and store IoT hub messages](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md) (Создание приложения-функции Azure и учетной записи хранения Azure для обработки и хранения сообщений Центра Интернета вещей).


