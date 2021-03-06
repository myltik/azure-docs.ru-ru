---
title: Доступ к API Служб мультимедиа Azure (CLI 2.0) | Документация Майкрософт
description: В этом руководстве приведены сведения о получении доступа к API Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a4a7c59e93b860245d67695de90fbae2becac3e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="access-azure-media-services-api-with-cli-20"></a>Доступ к API Служб мультимедиа Azure с помощью CLI 2.0
 
Чтобы подключиться к API Служб мультимедиа Azure, необходимо настроить аутентификацию субъекта-службы Azure AD. Приложение должно запросить токен Azure AD со следующими параметрами:

* Конечная точка клиента Azure AD.
* Универсальный код ресурса (URI) для ресурса служб мультимедиа.
* Универсальный код ресурса (URI) для ресурса REST служб мультимедиа.
* Значения приложения Azure AD: идентификатор и секрет клиента.

В этой статье показано, как с помощью CLI 2.0 создать приложение Azure AD и субъект-службу, а также как получить значения, необходимые при доступе к ресурсам Служб мультимедиа Azure.

## <a name="prerequisites"></a>предварительным требованиям 

Создайте учетную запись Служб мультимедиа Azure, как описано в [этом руководстве](create-account-cli-quickstart.md).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](http://portal.azure.com) и запустите **CloudShell**, чтобы выполнить команды CLI, приведенные на следующих шагах.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
