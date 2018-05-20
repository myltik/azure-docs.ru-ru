Теперь Azure поддерживает две функции отладки. Виртуальные машины Azure, созданные на основе модели развертывания с помощью Resource Manager, поддерживают выходные данные и снимки экрана консоли. 

При передаче собственного образа в Azure или даже при загрузке одного из образов платформ могут возникать проблемы с загрузкой виртуальной машины. Это может происходить по разным причинам. Новые функции позволяют легко диагностировать и восстанавливать виртуальную машину после сбоев загрузки.

На виртуальных машинах Linux выходные данные журнала консоли можно легко просмотреть на портале.

![Портал Azure](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Но Azure также позволяет просматривать снимки экранов виртуальных машин Windows и Linux из гипервизора.

![Ошибка](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Обе эти функции доступны на виртуальных машинах Azure во всех регионах. Обратите внимание, что отображение снимков экрана и выходных данных в учетной записи хранения может занять до 10 минут.

## <a name="common-boot-errors"></a>Распространенные ошибки загрузки

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Операционная система не найдена](https://support.microsoft.com/help/4010142)
- [Сбой при загрузке или INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Включение диагностики на новой виртуальной машине
1. При создании виртуальной машины на портале Azure из раскрывающегося списка выбора модели развертывания выберите **Azure Resource Manager**.
 
    ![Диспетчер ресурсов](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. В разделе **Параметры** включите функцию **Диагностика загрузки**, а затем выберите учетную запись хранения, в которую вы хотите поместить эти файлы данных диагностики.
 
    ![Создание виртуальной машины](./media/virtual-machines-common-boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > Функция диагностики загрузки не поддерживает учетную запись хранения уровня "Премиум". В случае использования учетной записи хранения уровня "Премиум" для диагностики загрузки при запуске виртуальной машины может произойти ошибка StorageAccountTypeNotSupported.
    >
    > 

3. При развертывании на основе шаблона Azure Resource Manager перейдите к ресурсу виртуальной машины и добавьте раздел профиля диагностики. Не забудьте добавить заголовок версии API 2015-06-15.

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. Профиль диагностики позволяет выбрать учетную запись хранения, в которую нужно поместить эти журналы.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Чтобы развернуть виртуальную машину c включенной функцией диагностики загрузки, просмотрите наш репозиторий.

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>Включение диагностики загрузки в существующей виртуальной машине 

Чтобы включить диагностику загрузки в существующей виртуальной машине, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com) и выберите виртуальную машину.
2. В разделе **Support + troubleshooting** (Поддержка и устранение неполадок) выберите **Диагностика загрузки** > **Параметры**, измените состояние на **Включено**, а затем выберите учетную запись хранения. 
4. Убедитесь, что выбран параметр "Диагностика загрузки", и сохраните изменения.

    ![Обновление имеющейся виртуальной машины](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

3. Чтобы изменения вступили в силу, перезапустите виртуальную машину.


