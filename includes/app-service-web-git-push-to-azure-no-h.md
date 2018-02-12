---
title: "включение файла"
description: "включение файла"
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: de3be6fcd9cd1bee4cfc590a41e69d4ae2a2468b
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
Вернитесь к _окну терминала (в локальном расположении)_ и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените фразу _&lt;paste\_copied\_url\_here>_ (вставьте скопированный url-адрес сюда) URL-адресом удаленного репозитория Git, который вы сохранили при [создании веб-приложения](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. При появлении запроса введите пароль, созданный в разделе на шаге [настройки пользователя развертывания](#configure-a-deployment-user) (а не используемый для входа на портал Azure).

```bash
git push azure master
```

Выполнение этой команды может занять несколько минут. При выполнении эта команда выводит приблизительно следующие сведения:
