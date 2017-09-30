## <a name="export-an-api-definition"></a>Импорт определения API
Вы создали определение OpenAPI функции при работе со статьей [Создание определения OpenAPI функции](../articles/azure-functions/functions-openapi-definition.md). Следующий шаг в этом процессе — экспорт определения API. Это позволит PowerApps и Microsoft Flow использовать его в пользовательском API.

> [!IMPORTANT]
> Помните, что необходимо войти в Azure с учетными данными, которые используются в клиентах PowerApps и Microsoft Flow. Это позволит Azure создать пользовательский API и сделать его доступным как для PowerApps, так и для Microsoft Flow.

1. На [портале Azure](https://portal.azure.com) щелкните имя приложения-функции (например, **function-demo-energy**) > **Функции платформы** > **Определение API**.

    ![определения API](media/functions-export-api-definition/api-definition.png)

1. Нажмите кнопку **Export to PowerApps + Flow** (Экспорт в PowerApps + Flow).

    ![Источник определения API](media/functions-export-api-definition/export-api-1.png)

1. В области справа используйте параметры, указанные в таблице ниже.

    |Настройка|Описание|
    |--------|------------|
    |**Режим экспорта**|Выберите **Express**, чтобы автоматически создать пользовательский API. При выборе значения **Вручную** экспортируется определение API, но затем вы должны вручную импортировать его в PowerApps и Microsoft Flow. См. дополнительные инструкции по [экспорту в PowerApps и Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Среда**|Выберите среду, в которую нужно сохранить пользовательский API. См. дополнительные сведения о [среде PowerApps](https://powerapps.microsoft.com/tutorials/environments-overview/) и [среде Microsoft Flow](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Имя пользовательского API**|Введите имя, например `Turbine Repair`.|
    |**Имя ключа API**|Введите имя, которое должны видеть создатели приложения и потока в интерфейсе пользовательского API. Обратите внимание, что в примере содержатся полезные сведения.|
 
    ![Экспорт в PowerApps и Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Нажмите кнопку **ОК**. После этого будет создан пользовательский API, а затем добавлен в указанную среду.