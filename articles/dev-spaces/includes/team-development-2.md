---
title: включение файла
description: включение файла
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367376"
---
### <a name="run-the-service"></a>Запуск службы

1. Чтобы запустить службу, нажмите клавишу F5 (или введите `azds up` в окне терминала). Служба автоматически запустится в выбранной среде `scott`. 
1. Убедитесь, что служба запущена в своей среде, еще раз выполнив команду `azds resource list`. Во-первых, вы увидите, что экземпляр `mywebapi` теперь выполняется в среде `scott` (версия, запущенная в `default`, по-прежнему выполняется, но не отображается). Во-вторых, URL-адрес точки доступа для `webfrontend` имеет префикс с текстом *scott.s.* Этот URL-адрес является уникальным для среды `scott`. Он указывает на то, что запросы, отправленные на URL-адрес scott, сначала будут направляться к службам в среде `scott`, а затем возвратятся к службам в среде `default`.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Эта встроенная возможность Azure Dev Spaces позволяет выполнять комплексное тестирование кода в общей среде, не требуя от каждого разработчика повторно создавать множество служб в своем пространстве. Для такой маршрутизации требуется, чтобы код приложения отправлял заголовки распространения, как показано на предыдущем шаге этого руководства.

## <a name="test-code-in-a-space"></a>Проверка кода в среде
Чтобы проверить свою новую версию `mywebapi` в сочетании с `webfrontend`, откройте в браузере URL-адрес общедоступной точки доступа (например, webfrontend) и перейдите на страницу About (Сведения). Отобразится новое сообщение.

Теперь удалите часть "scott.s" из URL-адреса и обновите браузер. Снова будет наблюдаться старое поведение (версия `mywebapi`, запущенная в `default`).