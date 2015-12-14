### План обслуживания приложения

Создает план обслуживания для размещения веб-приложения. Имя плана задается с помощью параметра **hostingPlanName**. Расположение плана совпадает с расположением веб-приложения. Ценовая категория и размер рабочего процесса задаются с помощью параметров **sku** и **workerSize**.

    {
      "apiVersion": "2014-06-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "name": "[parameters('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "numberOfWorkers": 1
      }
    },

<!---HONumber=AcomDC_1203_2015-->