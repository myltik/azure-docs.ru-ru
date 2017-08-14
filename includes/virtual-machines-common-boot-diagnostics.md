Теперь Azure поддерживает две функции отладки. Виртуальные машины Azure, созданные на основе модели развертывания с помощью Resource Manager, поддерживают выходные данные и снимки экрана консоли. 

При передаче собственного образа в Azure или даже при загрузке одного из образов платформ могут возникать проблемы с загрузкой виртуальной машины. Это может происходить по разным причинам. Новые функции позволяют легко диагностировать и восстанавливать виртуальную машину после сбоев загрузки.

На виртуальных машинах Linux выходные данные журнала консоли можно легко просмотреть на портале:

![Портал Azure](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
Но Azure также позволяет просматривать снимки экранов виртуальных машин Windows и Linux из гипервизора:

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
1. При создании виртуальной машины на портале предварительной версии в раскрывающемся списке выбора модели развертывания выберите **Azure Resource Manager**:
 
    ![Диспетчер ресурсов](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. Настройте параметр "Мониторинг", указав учетную запись хранения, в которую вы хотели бы поместить эти файлы диагностики.
 
    ![Создание виртуальной машины](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

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

## <a name="update-an-existing-virtual-machine"></a>Обновление имеющейся виртуальной машины ##

Диагностику загрузки можно также включить, обновив имеющуюся виртуальную машину на портале. Установите флажок "Диагностика загрузки" и нажмите кнопку "Сохранить". Чтобы изменения вступили в силу, перезапустите виртуальную машину.

![Обновление имеющейся виртуальной машины](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)

