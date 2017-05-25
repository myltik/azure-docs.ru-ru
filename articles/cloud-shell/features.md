---
title: "Функции Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Обзор функций Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e789283ab9b25f634c50b341ca882bbf9c70a225
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017

---

# <a name="features--tools-for-azure-cloud-shell"></a>Функции и средства Azure Cloud Shell
Azure Cloud Shell — это браузерная оболочка, предназначенная для разработки ресурсов Azure и управления ими.

Cloud Shell предлагает предварительно настроенную и доступную из браузера оболочку для управления ресурсами Azure, которая избавит вас от необходимости самостоятельно устанавливать ПО, управлять версиями и обслуживать компьютер.

Cloud Shell подготавливает компьютеры по запросу, в результате чего состояние компьютера не сохраняется между сеансами. Поскольку Cloud Shell создана для интерактивных сеансов, оболочки автоматически завершают работу после 10 минут бездействия.

## <a name="tools"></a>Средства
|Категория   |Имя   |
|---|---|
|Интерпретатор оболочки Linux|Bash<br> sh               |
|Инструменты Azure            |Azure CLI 2.0 и 1.0     |
|Текстовые редакторы           |vim<br> nano<br> emacs       |
|Система управления версиями         |git                    |
|Инструменты сборки            |make<br> maven<br> npm<br> pip         |
|Контейнеры             |Docker<br> Kubectl<br> Интерфейс командной строки DC/OS         |
|Базы данных              |Клиент MySQL<br> Клиент PostgreSQL<br> Служебная программа sqlcmd      |
|Другие                  |Клиент iPython |

## <a name="language-support"></a>Поддержка языков
|язык   |Version (версия)   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Node.js    |6.9.4      |
|Python     |2.7 и 3.5|

## <a name="secure-automatic-authentication"></a>Безопасная автоматическая аутентификация
Cloud Shell безопасно и автоматически выполняет аутентификацию доступа к учетной записи для Azure CLI 2.0.

## <a name="azure-files-persistence"></a>Сохраняемость файлов Azure
Поскольку Cloud Shell выделяется по запросу с использованием временного компьютера, локальные файлы за пределами каталога $Home и сведения о состоянии компьютера не сохраняются между сеансами.
Чтобы сохранять файлы между сеансами, при первом запуске Cloud Shell предлагается присоединить общую папку Azure.
По завершении настройки Cloud Shell будет автоматически присоединять хранилище для всех будущих сеансов.

Дополнительные сведения см. в статье [Сохранение файлов в Azure Cloud Shell](persisting-shell-storage.md).

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по Azure Cloud Shell](quickstart.md) 
[Azure CLI 2.0: Command reference - az](https://docs.microsoft.com/cli/azure/) (Справочник по командам az в Azure CLI 2.0)
