


Группа доступности позволяет поддерживать доступность виртуальных машин во время простоев, например связанных с обслуживанием. Размещение двух или более одинаково настроенных виртуальных машин в группе доступности создает избыточность, необходимую для обеспечения доступности приложений или служб, выполняемых на виртуальной машине. См. дополнительные сведения об [управлении доступностью виртуальных машин][Manage the availability of virtual machines].

Чтобы обеспечить постоянную доступность и эффективность работы приложения, рекомендуется использовать и группы доступности, и балансировку нагрузки конечных точек. См. дополнительные сведения о [балансировке нагрузки для служб инфраструктуры Azure][Load balancing for Azure infrastructure services].

Добавлять классические виртуальные машины в группы доступности можно одним из двух способов.

* [Вариант 1. Одновременное создание виртуальной машины и группы доступности][Option 1: Create a virtual machine and an availability set at the same time]. Затем добавляйте создаваемые виртуальные машины в эту группу.
* [Вариант 2. Добавление существующей виртуальной машины к группе доступности][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> В классической модели виртуальные машины, которые вы хотите поместить в одну группу доступности, должны относиться к одной облачной службе.
> 
> 

## <a id="createset"> </a>Вариант 1. Одновременное создание виртуальной машины и группы доступности
Для этого можно использовать портал Azure или команды Azure PowerShell.

Использование портала Azure

1. Перейдите на [портал Azure](https://portal.azure.com), если вы еще этого не сделали.
2. Щелкните **Создать ресурс** > **Вычисление**.
3. Выберите нужный образ виртуальной машины Marketplace. Можно создать виртуальную машину Windows или Linux.
4. После выбора виртуальной машины проверьте, что задана **классическая** модель развертывания, и нажмите кнопку **Создать**.
   
    ![Замещающий текст](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Введите имя виртуальной машины, имя пользователя и пароль (для компьютеров Windows) или открытый ключ SSH (для компьютеров Linux). 
6. Укажите размер виртуальной машины и нажмите кнопку **Выбрать** , чтобы продолжить.
7. Выберите пункты **Дополнительная настройка > Группа доступности**, а затем укажите группу доступности, в которую нужно добавить виртуальную машину.
   
    ![Замещающий текст](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Проверьте настройки конфигурации. Когда все будет готово, нажмите **Создать**.
9. Процесс создания виртуальной машины в Azure можно отслеживать в разделе **Виртуальные машины** , который можно выбрать в главном меню.

Чтобы использовать команды Azure PowerShell для создания виртуальной машины Azure и добавления ее в новую или существующую группу доступности, ознакомьтесь со статьей [Создание виртуальной машины Windows с использованием PowerShell и классической модели развертывания](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a id="addmachine"> </a>Вариант 2. Добавление существующей виртуальной машины к группе доступности
На портале Azure можно добавить существующие классические виртуальные машины к существующей группе доступности либо создать для них новую. (Учитывайте, что виртуальные машины из одной группы доступности должны входить в одну облачную службу.) Выполняемые действия практически идентичны. С помощью Azure PowerShell можно добавить виртуальную машину к существующей группе доступности.

1. Войдите на [портал Azure](https://portal.azure.com), если вы еще не сделали это.
2. В меню слева щелкните **Виртуальные машины (классические)**.
   
    ![Замещающий текст](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Выберите из списка имя виртуальной машины, которую хотите добавить к группе.
4. Выберите пункт **Группа доступности** в **параметрах** виртуальной машины.
   
    ![Замещающий текст](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Выберите группу доступности, в которую нужно добавить виртуальную машину. Виртуальная машина должна находиться в той же облачной службе, что и группа доступности.
   
    ![Замещающий текст](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Выберите команду **Сохранить**.

Чтобы использовать команды Azure PowerShell, откройте сеанс Azure PowerShell уровня администратора и выполните следующую команду. Для заполнителей (таких как &lt;VmCloudServiceName&gt;) замените все содержимое внутри кавычек, включая символы, на правильные имена.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Возможно, вам нужно будет перезапустить виртуальную машину, чтобы завершить ее добавление к группе доступности.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

