
#### Создание новой службы

1. Используя данные учетной записи Майкрософт, войдите на портал управления по этому URL-адресу: [http://azure.microsoft.com/](http://azure.microsoft.com/).

2. На портале управления щелкните **Создать** > **Службы данных** > **Диспетчер StorSimple** > **Быстрое создание**.

3. В открывшейся форме выполните следующие действия.
  1. В поле **Имя** укажите уникальное имя для службы. Здесь необходимо указать понятное имя, пригодное для идентификации службы. Имя может содержать от 2 до 50 символов, среди которых могут быть буквы, цифры и дефисы. Имя должно начинаться и заканчиваться буквой или цифрой.
  2. Укажите **Расположение** службы. В общем, выберите расположение, ближайшее к географическому региону, в котором вы хотите развернуть устройство. Кроме того, необходимо учитывать следующее: 
	 
		- If you have existing workloads in Azure that you also intend to deploy with your StorSimple device, you should use that datacenter.
		- Your StorSimple Manager service and Azure storage can be in two separate locations. In such a case, you are required to create the StorSimple Manager and Azure storage account separately. To create an Azure storage account, go to the Azure Storage service in the Management Portal and follow the steps in [Create an Azure Storage account](storage-create-storage-account.md#create-a-storage-account). After you create this account, add it to the StorSimple Manager service by following the steps in [Configure a new storage account for the service](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).
		 
  3. В раскрывающемся списке **Подписка** выберите подписку. Подписка привязана к учетной записи для выставления счетов. Это поле отсутствует, если у вас имеется только одна подписка.
  4. Выберите **Создать новую учетную запись хранения**, чтобы автоматически создать учетную запись хранения одновременно со службой. Эта учетная запись хранения будет иметь специальное имя, например storsimplebwv8c6dcnf. Если требуется хранить данные в другом расположении, снимите этот флажок. 
  5. Щелкните **Создать диспетчер StorSimple**, чтобы создать службу.

   ![создайте службу](./media/storsimple-create-new-service/HCS_CreateAService-include.png)

  Будет выполнено перенаправление на стартовую страницу **Служба**. Создание службы займет несколько минут. После успешного создания службы будет выведено уведомление и состояние службы сменится на **Активна**.
 
   ![создание службы](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

<!---HONumber=August15_HO8-->