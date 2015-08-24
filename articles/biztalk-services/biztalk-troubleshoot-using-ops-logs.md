<properties 
	pageTitle="Устранение неполадок в службах BizTalk с помощью журналов операций | Microsoft Azure" 
	description="Устранение неполадок в службах BizTalk с помощью журналов операций. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>


# Службы BizTalk: устранение неполадок с помощью журналов операций

## Что такое журналы операций
Журналы операций — это компонент служб управления, доступный на портале управления Azure, который позволяет просматривать журналы операций, выполняемых в службах Azure, в том числе службы BizTalk. Это позволяет просматривать исторические данные, относящиеся к операциям управления, которые выполнены для подписки на службу BizTalk 180 дней назад или позднее.

> [AZURE.NOTE]Эта функция собирает информацию журналов только для операций управления в службах BizTalk, например, время запуска службы, создания резервной копии и т. д. Такие операции отслеживаются независимо от того, выполняются ли они на портале управления Azure или с помощью [REST API службы BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Полный список операций, которые отслеживаются с помощью служб управления, см. в разделе [Операции, отслеживаемые с помощью служб управления Azure](#bizops).<br/><br/> Этот компонент не ведет журналы для действий, связанных со средой выполнения службы BizTalk (например, при обработке сообщения мостами и т. д.). Чтобы просмотреть эти журналы, используйте представление отслеживания на портале служб BizTalk. Дополнительные сведения см. в разделе [Отслеживание сообщений](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## Просмотр журналов операций служб BizTalk
1. На портале управления Azure выберите **Службы управления**, а затем выберите вкладку **Журналы операций**.
2. Можно фильтровать журналы по различным параметрам, таким как подписки, диапазон дат, тип службы (например, службы BizTalk), имя службы или состояние операции (например, «Успешно», «Ошибка»).
3. Щелкните флажок, чтобы просмотреть отфильтрованный список. На следующем изображении показаны действия, связанные с testbiztalkservice: ![Просмотр журналов операций][ViewLogs] 
4. Чтобы узнать больше о конкретной операции, выберите строку и щелкните **Подробности** на панели задач в нижней части страницы.


## <a name="bizops"></a>Операции, отслеживаемые с помощью служб управления Azure
В следующей таблице перечислены операции, которые отслеживаются с помощью служб управления Azure.

Имя операции | Задача
--- | ---
CreateBizTalkService | Операция создания новой службы BizTalk
DeleteBizTalkService | Операция удаления службы BizTalk
RestartBizTalkService | Операция перезапуска службы BizTalk
StartBizTalkService | Операция запуска службы BizTalk
StopBizTalkService | Операция остановки службы BizTalk
DisableBizTalkService | Операция отключения службы BizTalk
EnableBizTalkService | Операция включения службы BizTalk
BackupBizTalkService | Операция резервного копирования службы BizTalk
RestoreBizTalkService | Операция восстановления службы BizTalk из указанной резервной копии
SuspendBizTalkService | Операция приостановки службы BizTalk
ResumeBizTalkService | Операция возобновления службы BizTalk
ScaleBizTalkService | Операция масштабирования службы BizTalk вверх или вниз
ConfigUpdateBizTalkService | Операция обновления конфигурации службы BizTalk
ServiceUpdateBizTalkService | Операция обновления или возврата к предыдущей версии службы BizTalk
PurgeBackupBizTalkService | Операция удаления резервных копий службы BizTalk по истечении срока хранения


## См. также
- [Резервное копирование службы BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Восстановление службы BizTalk из резервной копии](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Службы BizTalk. Диаграмма выпусков Developer, Basic, Standard и Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Службы BizTalk: подготовка с использованием портала управления Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Службы BizTalk. Диаграмма состояния подготовки](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Службы BizTalk: вкладки «Панель мониторинга», «Монитор» и «Масштаб»](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Службы BizTalk: регулирование](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Службы BizTalk: имя и ключ издателя](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Как приступить к работе с пакетом SDK для служб BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 

<!---HONumber=August15_HO7-->