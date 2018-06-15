---
title: Отладка опубликованной облачной службы Azure с помощью Visual Studio и IntelliTrace | Документация Майкрософт
description: Узнайте, как выполнять отладку облачной службы с помощью Visual Studio и IntelliTrace.
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: 2ca15bd5ffa88d2e8053decf5b81c265b1d9c6e1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292563"
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Отладка опубликованной облачной службы Azure с помощью Visual Studio и IntelliTrace
С помощью IntelliTrace можно записывать в журнал расширенные отладочные сведения для экземпляра роли при его запуске в Azure. Если вам необходимо найти причину проблемы, можете использовать журналы IntelliTrace для пошагового выполнения кода из Visual Studio, как если бы он запускался в Azure. Фактически IntelliTrace записывает выполнение ключевого кода и данные среды при запуске приложения Azure в качестве облачной службы в Azure и позволяет воспроизводить записанные данные в Visual Studio. 

IntelliTrace можно использовать, если ваше приложение Azure предназначено для .NET Framework 4 или более поздней версии и если у вас установлена среда разработки Visual Studio Enterprise. IntelliTrace собирает сведения для ваших ролей Azure. Виртуальные машины для этих ролей всегда запускают 64-разрядные операционные системы.

В качестве альтернативы можно использовать [удаленную отладку](http://go.microsoft.com/fwlink/p/?LinkId=623041) для непосредственного подключения к облачной службе, запущенной в Azure.

> [!IMPORTANT]
> IntelliTrace предназначена только для отладки и не должна использоваться в рабочей среде.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Настройка приложения Azure для IntelliTrace
Чтобы включить IntelliTrace для приложения Azure, необходимо создать и опубликовать приложение из проекта Visual Studio Azure. Перед публикацией в Azure необходимо настроить IntelliTrace для приложения Azure. Если вы опубликовали приложение без настройки IntelliTrace, то необходимо еще раз опубликовать проект. Дополнительные сведения см. в статье [Публикация облачной службы с помощью инструментов Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Перед развертыванием приложения Azure убедитесь, что в качестве целей сборки проекта выбрана **Отладка**.

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и в контекстном меню выберите **Опубликовать**.
   
1. В диалоговом окне **Опубликовать приложение Azure** выберите подписку Azure и нажмите кнопку **Далее**.

1. На странице **Параметры** выберите вкладку **Дополнительные параметры**.

1. Установите флажок **Включить IntelliTrace**, чтобы собирать журналы IntelliTrace для приложения во время его публикации в облаке.
   
1. Чтобы настроить базовую конфигурацию IntelliTrace, щелкните ссылку **Параметры** рядом с параметром **Включить IntelliTrace**.

    ![Ссылка на "Параметры IntelliTrace"](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. В диалоговом окне **IntelliTrace Settings** можно указать, какие события будут записываться в журнал, требуется ли осуществлять сбор сведений о вызовах, для каких модулей и процессов необходимо записывать журналы и какой объем дискового пространства необходимо выделить для записи. Дополнительные сведения об IntelliTrace см. в разделе [Отладка с помощью IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Параметры IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Журнал IntelliTrace представляет собой кольцевой файл журнала максимального размера, указанного в настройках IntelliTrace (размер по умолчанию — 250 МБ). Журналы IntelliTrace записываются в файл в файловой системе виртуальной машины. При запросе журналов формируется моментальный снимок на данный момент времени и загружается на локальный компьютер.

После публикации облачной службы в Azure с помощью узла Azure в **обозревателе сервера** можно определить, был ли включен IntelliTrace, как показано на следующем рисунке:

![Обозреватель сервера: IntelliTrace включен](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Скачивание журналов IntelliTrace для экземпляра роли
С помощью Visual Studio можно скачать журналы IntelliTrace для экземпляра роли, выполнив следующие действия:

1. В **обозревателе сервера** разверните узел **Облачные службы** и найдите экземпляр роли, журналы которого необходимо скачать. 

1. Щелкните правой кнопкой мыши экземпляр роли и в контекстном меню выберите **Просмотр журналов IntelliTrace**. 

    ![Пункт меню "Просмотр журналов IntelliTrace"](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Журналы IntelliTrace будут загружены в файл в каталоге на локальном компьютере. При каждом запросе журналов IntelliTrace создается новый моментальный снимок. Во время скачивания журналов Visual Studio отображает ход выполнения операции в окне **Журнал действий Azure**. Как показано на следующем рисунке, элемент строки можно развернуть для более детального просмотра операции.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Во время загрузки журналов IntelliTrace можно продолжать работу в Visual Studio. После завершения скачивания журнала он открывается в Visual Studio.

> [!NOTE]
> Журналы IntelliTrace могут содержать исключения, которые платформа создает и в дальнейшем обрабатывает. Эти исключения формируются внутренним кодом платформы при обычном запуске роли, поэтому на них можно не обращать внимания.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
- [Параметры отладки облачных служб Azure](vs-azure-tools-debugging-cloud-services-overview.md)
- [Публикация облачной службы с помощью инструментов Azure](vs-azure-tools-publishing-a-cloud-service.md)