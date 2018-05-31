---
title: Устранение неполадок | Документация Майкрософт
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Быстрая разработка в Kubernetes с использованием контейнеров и микрослужб в Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199302"
---
# <a name="troubleshooting-guide"></a>Руководство по устранению неполадок

Это руководство содержит сведения о распространенных проблемах, которые могут возникнуть при использовании Azure Dev Spaces.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Ошибка "upstream connect error or disconnect/reset before headers" (ошибка вышестоящего подключения или отключение либо сброс до прохождения заголовков)
Вы можете увидеть эту ошибку при попытке получить доступ к службе. Например, при переходе по URL-адресу службы в браузере. 

### <a name="reason"></a>Причина 
Порт контейнера недоступен. Это может быть связано со следующим: 
* Процесс сборки или развертывания контейнера еще не завершен. Это может случиться, если вы запускаете `azds up` или отладчик, а затем пытаетесь получить доступ к контейнеру до его успешного развертывания.
* Конфигурация порта не согласуется в Dockerfile, диаграмме Helm и серверном коде, который открывает порт.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Если процесс сборки или развертывания еще не завершен, вы можете подождать 2–3 секунды и повторить попытку доступа к службе. 
1. Проверьте конфигурацию порта. Указанные номера портов должны **совпадать** во всех следующих ресурсах:
    * **Dockerfile:** задается инструкцией `EXPOSE`.
    * **[Диаграмма Helm](https://docs.helm.sh):** задается значениями `externalPort` и `internalPort` для службы (часто находится в файле `values.yml`).
    * Все порты, открытые в коде приложения, например в Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Файл конфигурации не найден
Вы запускаете `azds up`, и появляется следующая ошибка: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Причина
Необходимо запустить `azds up` из корневого каталога кода, который вы хотите выполнить, и инициализировать папку с кодом для запуска с Azure Dev Spaces.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Измените текущий каталог на корневую папку, содержащую код службы. 
1. Если у вас нет файла azds.yaml в папке кода, запустите `azds prep` для создания ресурсов Docker, Kubernetes и Azure Dev Spaces.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Ошибка "The pipe program 'azds' exited unexpectedly with code 126" (Программа канала AZDS неожиданно завершила работу с кодом 126)
Запуск отладчика VS Code иногда может привести к этой ошибке. Это известная проблема.

### <a name="try"></a>Попробуйте выполнить следующее.
1. Закройте и снова откройте VS Code.
2. Нажмите клавишу F5 еще раз.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Ошибка отладки "Configured debug type 'coreclr' is not supported" (Настроенный тип отладки "coreclr" не поддерживается)
При запуске отладчика VS Code возникает ошибка: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Причина
На компьютере, на котором ведется разработка, не установлено расширения VS Code для Azure Dev Spaces.

### <a name="try"></a>Попробуйте выполнить следующее.
Установите [расширение VS Code для Azure Dev Spaces](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Не удалось найти имя типа или пространства имен "MyLibrary"

### <a name="reason"></a>Причина 
Контекст сборки находится на уровне проекта или службы по умолчанию, поэтому используемый проект библиотеки невозможно найти.

### <a name="try"></a>Попробуйте выполнить следующее.
Что необходимо сделать:
1. В файле azds.yaml задайте для контекста сборки уровень решения.
2. Измените файлы Dockerfile и Dockerfile.develop для корректного обращения к CSPROJ-файлам в зависимости от нового контекста сборки.
3. Поместите DOCKERIGNORE-файл рядом с SLN-файлом и внесите необходимые изменения.

Вы можете найти пример по ссылке https://github.com/sgreenmsft/buildcontextsample.

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>Ошибка авторизации "Microsoft.ConnectedEnvironment/register/action"
Вы можете увидеть следующую ошибку при управлении Azure Dev Space, когда вы работаете в подписке Azure, для которой у вас нет доступа с правами владельца или участника.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Причина
Выбранная подписка Azure не зарегистрировала пространство имен Microsoft.ConnectedEnvironment.

### <a name="try"></a>Попробуйте выполнить следующее.
Пользователь с правами владельца или участника в подписке Azure может запустить следующую команду Azure CLI для регистрации пространства имен Microsoft.ConnectedEnvironment вручную:

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces не использует существующий файл Dockerfile для сборки контейнера 

### <a name="reason"></a>Причина
Настройте Azure Dev Spaces так, чтобы она указывала на конкретный файл Dockerfile в вашем проекте. Если Azure Dev Spaces не использует файл Dockerfile, который вы хотели использовать для сборки ваших контейнеров, то можно явно указать Azure Dev Spaces, где он находится. 

### <a name="try"></a>Попробуйте выполнить следующее.
Откройте файл `azds.yaml`, созданный Azure Dev Spaces в проекте. Используйте директиву `configurations->develop->build->dockerfile` для указания файла Dockerfile, который вы хотите использовать:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```