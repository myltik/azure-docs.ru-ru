---
title: Установка интерфейса командной строки контроллера домена или ОС | Документация Майкрософт
description: Установка интерфейса командной строки контроллера домена или ОС.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Контейнеры, микрослужбы, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: a8ea47f158c0d666340815d2e039995c7483257f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22877259"
---
> [!NOTE]
> Это необходимо для работы с кластерами ACS на основе контроллеров домена или ОС. Это не требуется для кластеров ACS под управлением Swarm.
> 
> 

Сначала [подключитесь к кластеру ACS на основе контроллеров домена или ОС](../articles/container-service/container-service-connect.md). Теперь вы можете установить интерфейс командной строки контроллера домена или операционной системы на клиентский компьютер с помощью приведенной ниже команды:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Если вы используете старую версию Python, можно заметить предупреждения о незащищенной платформе (InsecurePlatformWarnings). Эти предупреждения можно игнорировать.

Чтобы приступить к работе без перезагрузки оболочки, выполните следующую команду:

```bash
source ~/.bashrc
```

Этот шаг не нужно выполнять при запуске новой оболочки.

Теперь можно проверить, установлен ли интерфейс командной строки:

```bash
dcos --help
```