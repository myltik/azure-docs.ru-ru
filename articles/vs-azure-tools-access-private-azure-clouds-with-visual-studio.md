---
title: "Доступ к частным облакам Azure с помощью Visual Studio | Документация Майкрософт"
description: "Узнайте, как получить доступ к ресурсам частного облака с помощью Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b29a3299433e57709ea31de3c7849230ea09c09a
ms.lasthandoff: 03/21/2017


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Доступ к частным облакам Azure с помощью Visual Studio
По умолчанию Visual Studio поддерживает конечные точки REST общедоступных облаков Azure. В этой статье вы узнаете, как использовать сертификат частного облака для доступа к частному облаку и взаимодействия с ним через Visual Studio.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Получение доступа к частному облаку Azure из Visual Studio
1. Скачайте файл параметров публикации с [классического портала Azure](http://go.microsoft.com/fwlink/?LinkID=213885) для частного облака или запросите этот файл у администратора. В общедоступной версии Azure скачать файл можно по ссылке [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Имя скачанного файла должно иметь расширение `.publishsettings`.)

1. Запустите Visual Studio

1. В **обозревателе сервера** щелкните правой кнопкой мыши узел **Azure**, а затем выберите в контекстном меню пункт **Управление подписками и их фильтрация**.
   
    ![Команда "Управление подписками"](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. В диалоговом окне **Управление подписками Microsoft Azure** выберите вкладку **Сертификаты**, а затем нажмите кнопку **Импорт**.
   
    ![Импорт сертификатов Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. В диалоговом окне **Импорт подписок Microsoft Azure** нажмите кнопку **Обзор**.

    ![Кнопка "Обзор" в диалоговом окне "Импорт подписок Microsoft Azure"](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. В диалоговом окне **Открытие** найдите каталог, в котором вы сохранили PUBLISHSETTINGS-файл, выберите его, а затем нажмите кнопку **Открыть**.

    ![Выбор PUBLISHSETTINGS-файла](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Вернувшись в диалоговое окно **Импорт подписок Microsoft Azure** нажмите кнопку **Импорт**.

    ![Импорт PUBLISHSETTINGS-файла](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Сертификаты импортируются из PUBLISHSETTINGS-файла в Visual Studio, и теперь вы можете работать с ресурсами частного облака.
   
## <a name="next-steps"></a>Дальнейшие действия
- [Публикация в облачной службе Azure из Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [How to: Download and Import Publish Settings and Subscription Information](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx) (Практическое руководство. Скачивание и импорт параметров публикации и информации о подписке)

