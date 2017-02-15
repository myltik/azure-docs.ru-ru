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
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26fea09810e4a5ed1dc19123a5354905ec3e37ea


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Доступ к частным облакам Azure с помощью Visual Studio
## <a name="overview"></a>Обзор
По умолчанию Visual Studio поддерживает конечные точки REST общедоступных облаков Azure. Однако в некоторых случаях требуется использовать Visual Studio для работы с частным облаком Azure. Вы можете настроить Visual Studio для доступа к конечным точкам REST частного облака Azure с помощью сертификатов. Их можно получить из файла параметров публикации Azure.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Получение доступа к частному облаку Azure из Visual Studio
1. Скачайте файл параметров публикации с [классического портала Azure](http://go.microsoft.com/fwlink/?LinkID=213885) для частного облака или запросите этот файл у администратора. В общедоступной версии Azure скачать файл можно по ссылке [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (загруженный файл должен иметь расширение PUBLISHSETTINGS).
2. В Visual Studio в **обозревателе сервера** выберите узел **Azure**, а затем в контекстном меню выберите пункт **Управление подписками**.
   
    ![Команда "Управление подписками"](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)
3. В диалоговом окне **Управление подписками Microsoft Azure** откройте вкладку **Сертификаты**, а затем нажмите кнопку **Импорт**.
   
    ![Импорт сертификатов Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)
4. В диалоговом окне **Импорт подписок Microsoft Azure** найдите папку, в которой сохранен файл параметров публикации, выберите этот файл, а затем нажмите кнопку **Импорт**. Сертификаты из файла параметров публикации будут импортированы в Visual Studio. Теперь вы сможете работать с ресурсами частного облака.
   
    ![Импорт параметров публикации](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Дальнейшие действия
[Публикация в облачной службе Azure из Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[How to: Download and Import Publish Settings and Subscription Information](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx) (Практическое руководство. Скачивание и импорт параметров публикации и информации о подписке)




<!--HONumber=Nov16_HO3-->


