---
title: Создание универсальной платформы Windows (UWP) для использования в мобильных приложениях | Документация Майкрософт
description: Следуйте указаниям этого руководства, чтобы начать работу с серверной частью мобильных приложений Azure для разработки приложений универсальной платформы Windows (UWP) на C#, Visual Basic или JavaScript.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 508989e0e2daeac7922d61ff692ee4579139cfc8
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27594961"
---
# <a name="create-a-windows-app"></a>Создание приложения Windows
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как добавить облачную серверную службу в универсальную платформу Windows (UWP). Дополнительные сведения см. в статье [Общие сведения о мобильных приложениях](app-service-mobile-value-prop.md). Ниже приведены снимки экрана готового приложения.

![Готовое классическое приложение](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Выполняется на настольном компьютере.

![Готовое приложение для телефона](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Выполняется на телефоне.

Изучение этого руководства является необходимым условием для работы со всеми остальными руководствами, посвященными мобильным приложениям UWP.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio Community 2015] или более поздней версии.

## <a name="create-a-new-azure-mobile-app-backend"></a>Создание серверной части мобильного приложения Azure
Чтобы создать серверную часть мобильного приложения, выполните указанные ниже действия.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Итак, вы подготовили серверную часть мобильного Azure, которая может использоваться мобильными клиентскими приложениями. Теперь скачайте серверный проект со списком простых задач и опубликуйте его в Azure.

## <a name="configure-the-server-project"></a>Настройка серверного проекта
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Скачивание и выполнение клиентского проекта
Настроив серверную часть мобильного приложения, можно создать новое клиентское приложение или изменить существующее приложение, чтобы подключиться к Azure. В этом разделе вы загрузите проект шаблона UWP, который настроен для подключения к серверной части мобильного приложения.

1. Вернитесь в колонку **Быстрый запуск** серверной части мобильного приложения и последовательно выберите элементы **Создать новое приложение** > **Загрузить**. Затем извлеките сжатые файлы проекта на локальный компьютер.

    ![Загрузка проекта быстрого запуска Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)
2. (Необязательно) Добавьте в то же решение проект приложения UWP в качестве серверного проекта. Это упрощает отладку и тестирование приложения и серверной части в одном решении Visual Studio, если вам это потребуется. Чтобы добавить в решение проект приложения UWP, необходимо использовать Visual Studio 2015 или более поздней версии.
3. Задав приложение UWP как запускаемый проект, нажмите клавишу F5, чтобы развернуть и запустить приложение.
4. В приложении в поле **Insert a TodoItem** (Вставить TodoItem) введите содержательный текст, например *Работа с руководством*, и нажмите кнопку **Сохранить**.

    ![Полный быстрый запуск Windows — классическая версия](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    В результате будет отправлен запрос POST к серверной части нового мобильного приложения, размещенного в Azure.
5. (Необязательно) Остановите приложение и перезапустите его на другом устройстве или эмуляторе мобильных устройств.

    ![Полный быстрый запуск Windows — мобильная версия](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Обратите внимание, что данные, сохраненные на предыдущем этапе, загружаются из Azure после запуска приложения UWP.

## <a name="next-steps"></a>Дополнительная информация
* [Добавление аутентификации в приложение](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Узнайте больше о проверке подлинности пользователей приложения с помощью поставщика удостоверений.
* [Добавление push-уведомлений в приложение](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Узнайте, как добавить поддержку push-уведомлений в мобильное приложение и настроить в его серверной части использование концентраторов уведомлений Azure для отправки push-уведомлений.
* [Включение автономной синхронизации для приложения](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильного приложения. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением &mdash; просматривать, добавлять или изменять данные &mdash; даже при отсутствии подключения к сети.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203
