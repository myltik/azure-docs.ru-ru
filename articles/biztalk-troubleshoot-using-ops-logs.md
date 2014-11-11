<properties linkid="biztalk-troubleshoot-using-ops-logs" urlDisplayName="BizTalk Services: Troubleshoot using operation logs" pageTitle="BizTalk Services: Troubleshoot using ops logs | Azure" metaKeywords="" description="BizTalk Services: Troubleshoot using ops logs" metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Troubleshoot using ops logs" authors="mandia"  solutions="" writer="nitinme" manager="dwrede" editor="cgronlun"  />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Службы BizTalk: устранение неполадок с помощью журналов операций

Журналы операций — это компонент служб управления, доступный на портале управления Azure, который позволяет просматривать исторические журналы операций, выполняемых в службах Azure, включая службы BizTalk. Это позволяет просматривать исторические данные, относящиеся к операциям управления, которые выполнены для подписки на службу BizTalk вплоть до 180 дней назад.

<div class="dev-callout"><b>Примечание.</b>
<p>Этот компонент ведет журналы только для операций управления в службах BizTalk, например когда служба была запущена, когда выполнено резервное копирование и т. д. Такие операции отслеживаются независимо от того, выполняются они из портала управления Azure или с помощью <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dn232347.aspx">REST API-интерфейсов службы BizTalk</a>. Полный список операций, которые отслеживаются с помощью служб управления, см. в разделе <a href="#bizops">Операции, отслеживаемые с помощью служб управления Azure</a>.</p>
<p>Этот компонент не ведет журналы для действий, связанных со средой выполнения службы BizTalk (например, для при обработке сообщения мостами и т. д.). Чтобы просмотреть такие журналы, необходимо использовать представление отслеживания на портале служб BizTalk. Дополнительные сведения см. в разделе <a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">Отслеживание сообщений</a>.</p>
</div>

## <a name="viewlogs"></a>Просмотр журналов операций служб BizTalk

1. На портале управления Azure щелкните "Службы управления" и перейдите на вкладку "Журналы операций".
2. Можно фильтровать журналы по различным параметрам, таким как подписки, диапазон дат, тип службы (например, службы BizTalk), имя службы или состояние (операции, например "Успешно", "Ошибка").
3. Щелкните по галочке, чтобы просмотреть отфильтрованный список. На следующем рисунке показаны действия, связанные с тестовой службой BizTalk.
	![Просмотрите журналы действий][Просмотрите журналы действий]
4. Чтобы узнать больше о конкретной операции, выберите строку и щелкните по элементу **Сведения** в нижней части страницы.


## <a name="bizops"></a>Операции, отслеживаемые с помощью служб управления Azure

В следующей таблице перечислены операции, которые отслеживаются с помощью служб управления Azure.

<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">Операция создания новой службы BizTalk</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">Операция удаления службы BizTalk</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">Операция перезапуска службы BizTalk</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">Операция запуска службы BizTalk</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">Операция остановки службы BizTalk</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">Операция отключения службы BizTalk</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">Операция включения службы BizTalk</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">Операция резервного копирования службы BizTalk</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">Операция восстановления службы BizTalk из указанной резервной копии</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">Операция приостановки службы BizTalk</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">Операция возобновления службы BizTalk</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">Операция масштабирования службы BizTalk вверх или вниз</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">Операция обновления конфигурации службы BizTalk</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">Операция обновления или возврата к предыдущей версии службы BizTalk</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">Операция удаления резервных копий службы BizTalk по истечении срока хранения</td> 
</tr>
</table>

## См. также

-   [Резервное копирование службы BizTalk][Резервное копирование службы BizTalk]
-   [Восстановление службы BizTalk из резервной копии][Восстановление службы BizTalk из резервной копии]
-   [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium][Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium]
-   [Службы BizTalk: подготовка с использованием портала управления Windows Azure][Службы BizTalk: подготовка с использованием портала управления Windows Azure]
-   [Службы BizTalk: диаграмма состояния подготовки][Службы BizTalk: диаграмма состояния подготовки]
-   [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"][Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]
-   [Службы BizTalk: регулирование][Службы BizTalk: регулирование]
-   [Службы BizTalk: имя и ключ издателя][Службы BizTalk: имя и ключ издателя]
-   [Как приступить к работе с пакетом SDK для служб BizTalk Azure][Как приступить к работе с пакетом SDK для служб BizTalk Azure]

  [Просмотрите журналы действий]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
  [Резервное копирование службы BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=325584
  [Восстановление службы BizTalk из резервной копии]: http://go.microsoft.com/fwlink/p/?LinkID=325582
  [Службы BizTalk: диаграмма выпусков Developer, Basic, Standard и Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Службы BizTalk: подготовка с использованием портала управления Windows Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Службы BizTalk: диаграмма состояния подготовки]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Службы BizTalk: вкладки "Панель мониторинга", "Монитор" и "Масштаб"]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Службы BizTalk: регулирование]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Службы BizTalk: имя и ключ издателя]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Как приступить к работе с пакетом SDK для служб BizTalk Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302335
