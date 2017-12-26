## <a name="create-a-self-hosted-integration-runtime"></a>Создание локальной среды выполнения интеграции

В этом разделе вы создадите локальную среду выполнения интеграции и свяжете ее с локальным компьютером, на котором находится база данных SQL Server. Локальная среда выполнения интеграции — это компонент, который копирует данные из SQL Server на компьютере в хранилище BLOB-объектов Azure. 

1. Создайте переменную для имени среды выполнения интеграции. Используйте уникальное имя и запишите его. Оно будет использоваться далее в этом руководстве. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Создайте локальную среду выполнения интеграции. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Пример выходных данных:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 
3. Чтобы получить состояние созданной среды выполнения интеграции, выполните следующую команду. Убедитесь, что свойству **State** присвоено значение **NeedRegistration**. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Пример выходных данных:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. Чтобы получить ключи проверки подлинности для регистрации локальной среды выполнения интеграции в службе фабрики данных Azure в облаке, выполните следующую команду: 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Пример выходных данных:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Скопируйте один из ключей (без двойных кавычек), используемый для регистрации локальной среды выполнения интеграции, которую вы установите на компьютер на следующих шагах.  

## <a name="install-the-integration-runtime"></a>Установка среды выполнения интеграции
1. Если на вашем компьютере уже установлена среда выполнения интеграции, удалите ее в разделе **Установка и удаление программ**. 

2. [Скачайте](https://www.microsoft.com/download/details.aspx?id=39717) локальную среду выполнения интеграции на локальный компьютер под управлением Windows. Запустите установку.

3. На странице **приветствия мастера установки Microsoft Integration Runtime** нажмите кнопку **Далее**.

4. На странице **Лицензионное соглашение** примите условия использования и лицензионное соглашение и нажмите кнопку **Далее**.

5. На странице **Конечная папка** нажмите кнопку **Далее**.

6. На странице **Ready to install Microsoft Integration Runtime** (Все готово для установки Microsoft Integration Runtime) нажмите кнопку **Установить**.

7. Если появится предупреждающее сообщение о настройке для компьютера, который не используется, о переходе в спящий режим или режим гибернации, нажмите кнопку **ОК**.

8. Если появится страница **Электропитание**, закройте ее и перейдите на страницу установки.

9. На странице **Completed the Microsoft Integration Runtime Setup** (Мастер установки Microsoft Integration Runtime завершил работу) нажмите кнопку **Готово**.

10. На странице **Регистрация Integration Runtime (Self-hosted)** вставьте ключ, созданный в предыдущем разделе, и выберите **Зарегистрировать**. 

    ![Регистрация среды выполнения интеграции](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Когда локальная среда выполнения интеграции будет успешно зарегистрирована, вы увидите следующее сообщение:

    ![Успешно зарегистрирована](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. На странице **Новый узел Integration Runtime (Self-hosted)** нажмите кнопку **Далее**. 

    ![Страница "Новый узел Integration Runtime"](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. На странице **Коммуникационный канал интрасети** нажмите кнопку **Пропустить**. Выберите сертификацию TLS/SSL для защиты внутриузловой передачи данных в среде выполнения интеграции с несколькими узлами. 

    ![Страница "Коммуникационный канал интрасети"](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. На странице **Регистрация Integration Runtime (Self-hosted)** выберите **Запустить Configuration Manager**.

15. Когда узел будет подключен к облачной службе, отобразится следующая страница:

    ![Страница Node is connected (Узел подключен)](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Теперь проверьте возможность подключения к базе данных SQL Server.

    ![Вкладка «Диагностика»](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    а. На странице **Configuration Manager** перейдите на вкладку **Diagnostics** (Диагностика).

    b. В качестве типа источника данных выберите **SqlServer**.

    c. Введите имя сервера.

    d. Введите имя базы данных.

    д. Выберите режим проверки подлинности.

    f. Введите имя пользователя.

    ж. Введите пароль для этого имени пользователя,

    h. Нажмите кнопку **проверки**, чтобы убедиться, что эта среда выполнения интеграции может подключаться к серверу SQL Server. Если подключение установлено успешно, появится зеленый флажок. Если подключение не установлено, появится сообщение об ошибке. Исправьте ошибки и проверьте, может ли среда выполнения интеграции подключаться к SQL Server.    

    > [!NOTE]
    > Запишите значения типа проверки подлинности, сервера, базы данных, пользователя и пароль. Они будут использоваться далее в этом руководстве. 
    
