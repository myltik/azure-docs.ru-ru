<properties
	pageTitle="Использование удаления с возможностью восстановления в мобильных службах (Магазин Windows) | Microsoft Azure"
	description="Узнайте, как использовать функцию удаления с возможностью восстановления в мобильных службах Azure для вашего приложения"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="wesmc"/>

# Использование удаления с возможностью восстановления в мобильных службах

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


##Обзор

Для таблиц, созданных с помощью JavaScript или серверной части .NET, можно включить удаление с возможностью восстановления. Если используется обратимое удаление, в базу данных добавляется новый столбец *\_\_deleted* типа [SQL bit]. Если функция удаления с возможностью восстановления включена, операция удаления физически не удаляет строки из базы данных, а устанавливает для удаленного столбца значение TRUE.

При запросах записей в таблице с включенной функций удаления с возможностью восстановления удаленные строки по умолчанию не возвращаются в запросе. Для запроса этих строк необходимо передать параметр запроса *\_\_includeDeleted=true* в [операцию запроса REST](http://msdn.microsoft.com/library/azure/jj677199.aspx). В пакете SDK клиента .NET также можно использовать вспомогательный метод `IMobileServiceTable.IncludeDeleted()`.

Поддержка удаления с возможностью восстановления для серверной части .NET впервые реализована в версии 1.0.402 серверной части .NET для мобильных служб Microsoft Azure. Последние пакеты NuGet доступны здесь: [Серверная часть .NET для мобильных служб Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).


Ниже описаны потенциальные преимущества использования удаления с возможностью восстановления.

* При использовании компонента [Автономная синхронизация данных для мобильных служб] пакет SDK клиента автоматически запрашивает удаленные записи и удаляет их из локальной базы данных. Если функция удаления с возможностью восстановления не включена, необходимо написать дополнительный код для серверной части, который будет предоставлять пакету SDK клиента сведения, какие записи следует удалить из локального хранилища. В противном случае локальное хранилище клиента и серверная часть не будут согласованы, и для очистки локального хранилища потребуется вызвать метод `PurgeAsync()` клиента.
* В некоторых приложениях существует требование никогда не удалять данные физически либо удалять данные только после их аудита. В таком сценарии удобно использовать функцию удаления с возможностью восстановления.
* Обратимое удаление можно использовать для реализации функции "отмены удаления", чтобы можно было восстанавливать случайно удаленные данные. Однако обратимо удаленные записи занимают место в базе данных, поэтому рекомендуется создать запланированное задание для периодического необратимого удаления обратимо удаленных записей. Примеры см. в разделах [Использование удаления с возможностью восстановления в серверной части .NET] и [Использование удаления с возможностью восстановления в серверной части JavaScript]. Кроме того, клиентский код должен периодически вызывать функцию `PurgeAsync()`, чтобы необратимо удаленные записи не оставались в локальном хранилище данных устройства.





##Включение удаления с возможностью восстановления для серверной части .NET

Поддержка удаления с возможностью восстановления для серверной части .NET впервые реализована в версии 1.0.402 серверной части .NET для мобильных служб Microsoft Azure. Последние пакеты NuGet доступны здесь: [Серверная часть .NET для мобильных служб Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).

В следующем пошаговом руководстве описано включение удаления с возможностью восстановления для мобильной службы серверной части .NET.

1. Откройте проект серверной мобильной службы .NET в Visual Studio.
2. Щелкните правой кнопкой мыши проект серверной части .NET и выберите **Управление пакетами NuGet**.
3. В диалоговом окне диспетчера пакетов щелкните **Nuget.org** в разделе обновлений и установите версию 1.0.402 (или более позднюю) пакетов NuGet для [серверной части.NET мобильных служб Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=513165).
3. В обозревателе решений для Visual Studio разверните узел **Контроллеры** в проекте серверной части .NET и откройте источник контроллера. Например, *TodoItemController.cs*.
4. В методе `Initialize()` контроллера передайте параметр `enableSoftDelete: true` в конструктор EntityDomainManager.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


##Включение удаления с возможностью восстановления для серверной части JavaScript

На странице создания новой таблицы для мобильной службы можно включить удаление с возможностью восстановления.

![][2]

Включение удаления с возможностью восстановления для существующей таблицы в серверной части JavaScript:

1. Щелкните свою мобильную службу на [классическом портале Azure]. Затем перейдите на вкладку "Данные".
2. На странице данных щелкните для выбора нужной таблицы. На панели команд нажмите кнопку **Включить удаление с возможностью восстановления**. Если в таблице уже включена эта функция, кнопка будет недоступна, но на вкладке **Обзор** или **Столбцы** таблицы будет виден столбец *\_\_deleted*.

    ![][0]

    Чтобы выключить обратимое удаление для таблицы, перейдите на вкладку **Столбцы**, щелкните столбец *\_\_deleted* и нажмите кнопку **Удалить**.

    ![][1]

## <a name="using-with-dotnet"></a>Использование обратимого удаления для серверной части .NET


Следующее запланированное задание очищает записи, удаленные с возможностью восстановления, которые созданы более месяца назад:

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;

        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }

        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);

            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();

            return Task.FromResult(true);
        }
    }

Дополнительные сведения о планировании заданий в мобильных службах серверной части .NET см. в [статье о планировании повторяющихся заданий в мобильных службах сервера JavaScript](mobile-services-dotnet-backend-schedule-recurring-tasks.md).




##Использование удаления с возможностью восстановления для серверной части JavaScript

С помощью сценариев таблицы можно добавить логику функции удаления с возможностью восстановления с помощью мобильных служб сервера JavaScript.

Для обнаружения запроса о восстановлении используйте свойство "undelete" в сценарии обновления таблицы:

    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
Чтобы включить удаленные записи в результат запроса в сценарии, установите для параметра "includeDeleted" значение true:

    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Для извлечения удаленных записей в запросе HTTP добавьте параметр запроса "\_\_includedeleted=true":

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### Пример запланированного задания для очистки записей, удаленных с возможностью восстановления.

Пример запланированного задания, которое удаляет записи с последним обновлением до определенной даты:

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

Дополнительные сведения о планировании заданий в мобильных службах серверной части JavaScript см. в [статье о планировании повторяющихся заданий в мобильных службах сервера JavaScript](mobile-services-schedule-recurring-tasks.md).





<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[SQL bit]: http://msdn.microsoft.com/library/ms177603.aspx
[Автономная синхронизация данных для мобильных служб]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[классическом портале Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_1210_2015-->