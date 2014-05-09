<properties linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Добавление ВМ в виртуальную сеть" pageTitle="Добавление виртуальной машины в виртуальную сеть — Windows Azure" metaKeywords="" description="Руководство, в котором показано, как создать учетную запись хранения и виртуальную машину (ВМ), добавляемые к виртуальной сети Windows Azure." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Добавление виртуальной машины в виртуальную сеть" authors=""  solutions="" writer="" manager="" editor=""  />





<h1 id="vnet3">Добавление виртуальной машины в виртуальную сеть</h1>

<!--SOMEWHERE IN THIS TUTORIAL I NEED TO XREF TO THE OTHER VMACHINE TUTORIAL -->

В этом пошаговом руководстве перечислены действия по созданию учетной записи хранения Windows Azure и виртуальной машины (ВМ) для добавления к [виртуальной сети](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx).

В данном учебнике предполагается, что у читателя нет опыта использования платформы Windows Azure.

<div class="dev-callout"> 
<b>Важно!</b>

<p>Если планируется создание ВМ для установки нового леса Active Directory, следуйте инструкциям в разделе <a href="../active-directory-forest/">Установка нового леса Active Directory в Windows Azure</a>.</p>
</div>


## Цели ##

В этом руководстве вы узнаете следующее:

-  <a href="#CreateStorageAcct">Как создавать учетную запись хранения</a>

-  <a href="#CreateVM">Как создавать виртуальную машину и развертывать ее в виртуальной сети</a>

## Предварительные требования ##

- Изучите одно из следующих руководств: 

	-  [Создание виртуальной сети в Windows Azure](/ru-ru/manage/services/networking/create-a-virtual-network/)

		-ИЛИ- 
	-  [Создание виртуальной сети для подключений между организациями](/ru-ru/manage/services/networking/cross-premises-connectivity/)

- Учетная запись Windows Live как минимум с одной действительной активной подпиской.	

- Названия руководств [Создание виртуальной сети в Windows Azure](/ru-ru/manage/services/networking/create-a-virtual-network/) или [Создание виртуальной сети для подключений между организациями](/ru-ru/manage/services/networking/cross-premises-connectivity/):

	-	Территориальные группы, назначенные для виртуальной сети.

	-	Название виртуальной сети.

	-   Название подсети (подсетей).

## <a name="CreateStorageAcct">Создайте учетную запись хранения</a> ##

1.	После создания виртуальной сети через [портал управления Windows Azure](http://manage.windowsazure.com/) в левом нижнем углу экрана выберите **Создать**.

	![NewStorAcct](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.	В области навигации нажмите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ** И **БЫСТРО СОЗДАТЬ**.

	![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.	Введите следующие сведения и установите флажок в правом нижнем углу экрана.

-  **URL-адрес:** введите *yourstorage*.

-  **РЕГИОН ИЛИ ТЕРРИТОРИАЛЬНАЯ ГРУППА:** в раскрывающемся списке выберите **YourAffinityGroup**.

-  **ВКЛЮЧИТЬ ГЕОРЕПЛИКАЦИЮ:** не снимайте этот флажок.
 
	![CreateNewAcct](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

4.	После завершения процесса на странице **Хранилище** для столбца **СОСТОЯНИЕ** будет показано состояние **Онлайн**.
 
	![NewStorAcctCreated](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)


## <a name="CreateVM">Создайте виртуальную машину и выполните ее развертывание в виртуальной сети</a> ##
**Для создания виртуальной машины и ее развертывания в виртуальной сети:**

1.	После создания учетной записи хранения в левом нижнем углу экрана нажмите **Создать**.

	![NewVM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)


2.	На панели навигации нажмите **СРЕДА ВЫПОЛНЕНИЯ ПРИЛОЖЕНИЙ**, **ВИРТУАЛЬНАЯ МАШИНА**, а затем нажмите **ИЗ КОЛЛЕКЦИИ**.
 
	![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)


3.	На экране **Выбор ОС виртуальной машины** выберите **Windows Server 2008 R2 SP1, октябрь 2012 г.** (или последнюю из доступных версий) и нажмите кнопку "Далее".
 
	![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)


4.	На экране **Конфигурация виртуальной машины** укажите следующие сведения и нажмите кнопку "Далее". 
	<!-- НУЖНО ЛИ ГОВОРИТЬ ПОЛЬЗОВАТЕЛЯМ О НЕОБХОДИМОСТИ ЗАПИСЫВАТЬ ИМЯ ПОЛЬЗОВАТЕЛЯ И ПАРОЛЬ?? -->

	**Совет.** Запишите свое имя пользователя и пароль, поскольку эти учетные данные будут использоваться для входа в систему на новой виртуальной машине.

-  **ИМЯ ВИРТУАЛЬНОЙ МАШИНЫ:** введите *YourVMachine*.

-  **ИМЯ НОВОГО ПОЛЬЗОВАТЕЛЯ:** только для чтения.

-  **НОВЫЙ ПАРОЛЬ:** введите надежный пароль.

-  **ПОДТВЕРЖДЕНИЕ ПАРОЛЯ:** повторите пароль.

-  **РАЗМЕР:** выберите **Small**.
 
	![VMConfig](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

5.	На экране **Режим виртуальной машины** укажите следующие сведения и нажмите кнопку "Далее".

-  **Автономная виртуальная машина:** оставьте этот флажок установленным.

-  **DNS-ИМЯ:** введите *yourcloudapplication*.

-  **УЧЕТНАЯ ЗАПИСЬ ХРАНЕНИЯ:** выберите **yourstorage**.

-  **РЕГИОН ИЛИ ТЕРРИТОРИАЛЬНАЯ ГРУППА/ВИРТУАЛЬНАЯ СЕТЬ:** в раскрывающемся списке выберите **YourVirtualNetwork**.
 
	![VMMode](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

6.	На экране **Параметры виртуальной машины** укажите следующие сведения и нажмите на кнопку флажка. Будет создана виртуальная машина. Создание новой виртуальной машины может занять до 10 минут.
	<!-- НУЖНО ПРОВЕРИТЬ, СКОЛЬКО ВРЕМЕНИ В СРЕДНЕМ ЗАНИМАЕТ СОЗДАНИЕ ВИРТУАЛЬНОЙ МАШИНЫ -->

-  **ГРУППА ДОСТУПНОСТИ:** выберите **none**.

-  **ПОДСЕТИ ВИРТУАЛЬНОЙ СЕТИ:** выберите **FrontEndSubnet**.
	
	<div class="dev-callout"> 
	<b>ПРИМЕЧАНИЕ</b> 

	<p>Необходимо выбрать по крайней мере одну подсеть; при этом НЕ ВЫБИРАЙТЕ подсеть шлюза.</p>
	</div> 
 
	![Параметры ВМ](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

7.	После создания виртуальной машины ее **СОСТОЯНИЕ** на экране виртуальных машин будет отображаться как **Выполняется**.
 
	![Экземпляры ВМ](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)


8.	В области навигации щелкните **ВСЕ ЭЛЕМЕНТЫ**. Отобразятся все созданные объекты и их текущее состояние.
 
	![AllTab](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

## Дальнейшие действия ##
Установка дополнительного контроллера домена для локального домена Active Directory на созданную виртуальную машину описывается в разделе [Установка реплики контроллера домена Active Directory в виртуальной сети Windows Azure](/ru-ru/manage/services/networking/replica-domain-controller/).

## См. также

-  [Виртуальная сеть Windows Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156007.aspx)

-  [Настройка виртуальной сети с помощью файлов конфигурации сети](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj156097.aspx)

<!-- LINKS -->

[wa_com]: http://manage.windowsazure.com/
[Tut2_VN]: ..Tutorial2_CreateVNetCrossPrem 
[Tut1_VN]: ..Tutorial1_CreateVirtualNetwork
























