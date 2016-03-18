<properties 
   pageTitle="Доступ к частным облакам Azure с помощью Visual Studio | Microsoft Azure"
   description="Узнайте, как получить доступ к ресурсам частного облака с помощью Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/17/2015"
   ms.author="tarcher" />

# Доступ к частным облакам Azure с помощью Visual Studio

##Обзор

По умолчанию Visual Studio поддерживает конечные точки REST общедоступных облаков Azure. Однако в некоторых случаях требуется использовать Visual Studio для работы с частным облаком Azure. Вы можете настроить Visual Studio для доступа к конечным точкам REST частного облака Azure с помощью сертификатов. Их можно получить из файла параметров публикации Azure.

## Получение доступа к частному облаку Azure из Visual Studio

1. Скачайте файл параметров публикации на портале управления частным облаком или запросите его у администратора. В общедоступной версии Azure скачать файл можно по ссылке [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/) (загруженный файл должен иметь расширение PUBLISHSETTINGS).

1. В **обозревателе серверов** в Visual Studio выберите узел **Azure**, а затем в контекстном меню выберите пункт **Управление подписками**.

    ![Команда «Управление подписками»](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. В диалоговом окне **Управление подписками Microsoft Azure** откройте вкладку **Сертификаты**, а затем нажмите кнопку **Импорт**.

    ![Импорт сертификатов Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. В диалоговом окне **Импорт подписок Microsoft Azure** найдите папку, в которой сохранен файл параметров публикации, выберите этот файл, а затем нажмите кнопку **Импорт**. Сертификаты из файла параметров публикации будут импортированы в Visual Studio. Теперь вы сможете работать с ресурсами частного облака.

    ![Импорт параметров публикации](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## Дальнейшие действия

[Публикация в облачной службе Azure из Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Практическое руководство. Загрузка и импорт параметров публикации и сведений о подписке](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

<!---HONumber=AcomDC_1223_2015-->