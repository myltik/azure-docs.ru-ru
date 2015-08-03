## Развертывание с помощью PowerShell

1. Войдите в свою учетную запись Azure.

          Add-AzureAccount

   После предоставления учетных данных команда возвращает информацию о вашей учетной записи.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Если у вас несколько подписок, укажите идентификатор подписки, которую вы хотите использовать для развертывания. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Переключитесь на модуль диспетчера ресурсов Azure.

          Switch-AzureMode AzureResourceManager

4. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Введите имя группы ресурсов и расположение, необходимое для решения.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Появится сводка по новой группе ресурсов.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Чтобы создать новое развертывание для группы ресурсов, выполните команду **New-AzureResourceGroupDeployment** и укажите необходимые параметры. Параметры будут включать в себя имя развертывания, имя группы ресурсов, путь или URL-адрес созданного шаблона и другие параметры, необходимые для вашего сценария. 
   
   Для предоставления значений параметров доступны следующие способы:
   
   - Использование встроенных параметров.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Использование объекта параметра.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Использование файла параметров.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  После развертывания группы ресурсов вы увидите сводку по развертыванию.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Вот как можно получить информацию о сбоях развертывания.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Вот как можно получить подробную информацию о сбоях развертывания.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

<!---HONumber=July15_HO4-->