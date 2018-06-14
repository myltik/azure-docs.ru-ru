---
title: Приступая к работе с мобильными приложениями службы приложений Azure для приложений Xamarin.iOS | Документация Майкрософт
description: Этот учебник поможет приступить к использованию мобильных приложений в разработке для Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8b890630d352619da86c3017426e24f55ef016d9
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592717"
---
# <a name="create-a-xamarinios-app"></a>Создание приложения Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение Xamarin.iOS с помощью серверной части мобильного приложения Azure.  Вы создадите новую серверную часть мобильного приложения и простое приложение Xamarin.iOS *Todo list*, в котором в Azure хранятся данные приложения.

Завершение этого учебника является необходимым условием для других учебников по Xamarin.iOS, посвященных использованию функции мобильных приложений в службе приложений Azure.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с данным руководством вам потребуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, зарегистрируйтесь для получения бесплатной пробной версии Azure и получите до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio с Xamarin. Инструкции см. в статье об [установке и настройке Visual Studio и Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* Компьютер Mac с установленным ПО XCode версии 7.0 или выше и Xamarin Studio Community. См. статьи об [установке и настройке Visual Studio и Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) и [установке, настройке и проверке для пользователей Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-an-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure
Чтобы создать серверную часть мобильного приложения, выполните указанные ниже действия.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Настройка серверного проекта
Итак, вы подготовили серверную часть мобильного Azure, которая может использоваться мобильными клиентскими приложениями. Теперь скачайте серверный проект со списком простых задач и опубликуйте его в Azure.

Выполните следующие действия, чтобы настроить серверный проект для использования серверной части .NET или Node.js.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Скачивание и запуск приложения Xamarin.iOS
1. В окне браузера откройте [портал Azure] .
2. В колонке параметров мобильного приложения щелкните **Приступая к работе** > **Xamarin.iOS**. На этапе 3 нажмите кнопку **Создать приложение** (если вы еще не сделали этого).  Затем нажмите кнопку **Загрузить** .

      После этого клиентское приложение, которое подключается к серверной части мобильной службы, будет скачано. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.
3. Извлеките скачанный проект и откройте его в Xamarin Studio (или Visual Studio).

    ![][9]

    ![][8]
4. Нажмите клавишу F5, чтобы выполнить сборку проекта и запустить приложение в эмуляторе iPhone.
5. В приложении введите содержательный текст, например *Изучение Xamarin*, и нажмите кнопку **+**.

    ![][10]

    Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются серверной частью мобильной службы, а данные отображаются в списке.

> [!NOTE]
> Код, который обращается к серверной части вашей мобильной службы для запроса и вставки данных, можно просмотреть в файле C# QSTodoService.cs.
>
>

## <a name="next-steps"></a>Дополнительная информация
* [Включение автономной синхронизации для мобильного приложения Xamarin.Android](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Добавление проверки подлинности в приложение](app-service-mobile-xamarin-ios-get-started-users.md)
* [Добавление push-уведомлений в приложение Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Использование управляемого клиента для мобильных приложений Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[портал Azure]: https://portal.azure.com/
