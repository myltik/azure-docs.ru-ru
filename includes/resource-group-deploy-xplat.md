## Развертывание с помощью Azure CLI

1. Войдите в свою учетную запись Azure.

        azure login

  После предоставления учетных данных команда возвращает результат вашего входа.

        ...
        info:    login command OK

2. Если у вас несколько подписок, укажите идентификатор подписки, которую вы хотите использовать для развертывания.

        azure account set <YourSubscriptionNameOrId>

3. Переключитесь на модуль диспетчера ресурсов Azure.

        azure config mode arm

   Вы получите подтверждение нового режима.

        info:     New mode is arm

4. Создайте группу ресурсов, если у вас нет существующей группы ресурсов. Введите имя группы ресурсов и расположение, необходимое для решения.

        azure group create -n ExampleResourceGroup -l "West US"

   Появится сводка по новой группе ресурсов.

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Чтобы создать новое развертывание для группы ресурсов, выполните следующую команду и укажите необходимые параметры. Параметры будут включать в себя имя развертывания, имя группы ресурсов, путь или URL-адрес созданного шаблона и другие параметры, необходимые для вашего сценария.

   Для предоставления значений параметров доступны следующие способы:

   - Использование встроенных параметров и локального шаблона.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Использование встроенных параметров и ссылки на шаблон.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Использование файла параметров.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  После развертывания группы ресурсов вы увидите сводку по развертыванию.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Вот как можно получить информацию о последнем развертывании.

         azure group log show -l ExampleResourceGroup

7. Вот как можно получить подробную информацию о сбоях развертывания.

         azure group log show -l -v ExampleResourceGroup

<!---HONumber=July15_HO2-->