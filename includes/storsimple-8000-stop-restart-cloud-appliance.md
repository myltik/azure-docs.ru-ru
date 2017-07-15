#### Остановка и запуск облачного устройства
<a id="to-stop-and-start-a-cloud-appliance" class="xliff"></a>

1. Чтобы остановить облачное устройство, перейдите к виртуальной машине данного устройства.
    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. На панели команд щелкните **Остановить**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. При появлении запроса на подтверждение нажмите кнопку **Да**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. При остановке виртуальная машина освобождается. Во время остановки облачное устройство переходит в состояние **Отмена выделения**. После остановки облачного устройства его состояние будет **Остановлено (освобождено)**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. После остановки виртуальной машины нажмите кнопку **Запустить** (кнопка становится доступной), чтобы инициировать запуск. После запуска облачного устройства его состояние будет **Запущено**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Используйте следующие командлеты для остановки и запуска облачного устройства.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### Перезапуск облачного устройства
<a id="to-restart-a-cloud-appliance" class="xliff"></a>

Чтобы перезапустить облачное устройство, перейдите к виртуальной машине данного устройства. На панели команд нажмите кнопку **Перезапустить**. При появлении запроса подтвердите перезапуск. Когда облачное устройство будет готово к использованию, его состояние будет **Работает**.

![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Используйте следующий командлет для перезапуска облачного устройства.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

