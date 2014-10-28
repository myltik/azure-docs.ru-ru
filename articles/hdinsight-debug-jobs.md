<properties linkid="manage-services-hdinsight-debug-error-messages" urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Debug Hadoop in HDInsight: Error messages | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Отладка Hadoop в HDInsight: Сообщения об ошибках

## Введение

Сообщения об ошибках, перечисленные в этом разделе, призваны помочь пользователям Hadoop в Azure HDInsight понять возможные ошибки, которые могут возникать при администрировании службы с помощью Azure PowerShell, и уведомить их о действиях, которые можно предпринять для устранения таких ошибок.

Некоторые из этих сообщений об ошибке также могут отображаться на портале Azure, когда он используется для управления кластерами HDinsight. Существуют и другие сообщения об ошибках, которые менее подробны в связи с ограниченными возможностями по устранению таких ошибок в данном контексте. В контекстах, где подобные действия осуществимы, выводятся другие сообщения об ошибках. Например, при нарушении ограничений для параметра выводится всплывающее сообщение в правой стороне поля, где было введено значение. В этом случае было запрошено избыточное число узлов данных. Для устранения ошибки следует снизить это число до разрешенного значения (не более 22).

![HDI.Debugging.ErrorMessages.Portal][HDI.Debugging.ErrorMessages.Portal]

Ошибки, с которыми пользователь может столкнуться в Azure PowerShell или на портале Azure, отображаются в списке, упорядоченном по алфавиту, в разделе [Ошибки HDInsight][Ошибки HDInsight]. Там они связаны с записью в разделе [Описание и устранение ошибок][Описание и устранение ошибок], где приводятся следующие сведения об ошибке:

-   **Описание**: сообщение об ошибке, отображаемое для пользователя
-   **Устранение**: действия, которые требуется выполнить для устранения этой ошибки.

### Ошибки HDInsight

[AtleastOneSqlMetastoreMustBeProvided][AtleastOneSqlMetastoreMustBeProvided]

[AzureRegionNotSupported][AzureRegionNotSupported]

[ClusterContainerRecordNotFound][ClusterContainerRecordNotFound]

[ClusterDnsNameInvalidReservedWord][ClusterDnsNameInvalidReservedWord]

[ClusterNameUnavailable][ClusterNameUnavailable]

[ClusterUserNameInvalid][ClusterUserNameInvalid]

[ClusterUserNameInvalidReservedWord][ClusterUserNameInvalidReservedWord]

[ContainerNameMisMatchWithDnsName][ContainerNameMisMatchWithDnsName]

[DataNodeDefinitionNotFound][DataNodeDefinitionNotFound]

[DeploymentDeletionFailure][DeploymentDeletionFailure]

[DnsMappingNotFound][DnsMappingNotFound]

[DuplicateClusterContainerRequest][DuplicateClusterContainerRequest]

[DuplicateClusterInHostedService][DuplicateClusterInHostedService]

[FailureToUpdateDeploymentStatus][FailureToUpdateDeploymentStatus]

[HdiRestoreClusterAltered][HdiRestoreClusterAltered]

[HeadNodeConfigNotFound][HeadNodeConfigNotFound]

[HeadNodeConfigNotFound][HeadNodeConfigNotFound]

[HostedServiceCreationFailure][HostedServiceCreationFailure]

[HostedServiceHasProductionDeployment][HostedServiceHasProductionDeployment]

[HostedServiceNotFound][HostedServiceNotFound]

[HostedServiceWithNoDeployment][HostedServiceWithNoDeployment]

[InsufficientResourcesCores][InsufficientResourcesCores]

[InsufficientResourcesHostedServices][InsufficientResourcesHostedServices]

[InternalErrorRetryRequest][InternalErrorRetryRequest]

[InvalidAzureStorageLocation][InvalidAzureStorageLocation]

[InvalidNodeSizeForDataNode][InvalidNodeSizeForDataNode]

[InvalidNodeSizeForHeadNode][InvalidNodeSizeForHeadNode]

[InvalidRightsForDeploymentDeletion][InvalidRightsForDeploymentDeletion]

[InvalidStorageAccountBlobContainerName][InvalidStorageAccountBlobContainerName]

[InvalidStorageAccountConfigurationSecretKey][InvalidStorageAccountConfigurationSecretKey]

[InvalidVersionHeaderFormat][InvalidVersionHeaderFormat]

[MoreThanOneHeadNode][MoreThanOneHeadNode]

[OperationTimedOutRetryRequest][OperationTimedOutRetryRequest]

[ParameterNullOrEmpty][ParameterNullOrEmpty]

[PreClusterCreationValidationFailure][PreClusterCreationValidationFailure]

[RegionCapabilityNotAvailable][RegionCapabilityNotAvailable]

[StorageAccountNotColocated][StorageAccountNotColocated]

[SubscriptionIdNotActive][SubscriptionIdNotActive]

[SubscriptionIdNotFound][SubscriptionIdNotFound]

[UnableToResolveDNS][UnableToResolveDNS]

[UnableToVerifyLocationOfResource][UnableToVerifyLocationOfResource]

[VersionCapabilityNotAvailable][VersionCapabilityNotAvailable]

[VersionNotSupported][VersionNotSupported]

[VersionNotSupportedInRegion][VersionNotSupportedInRegion]

[WasbAccountConfigNotFound][WasbAccountConfigNotFound]

## <span id="discription-mitigation-errors"></span></a>Диагностика и устранение ошибок

### <span id="AtleastOneSqlMetastoreMustBeProvided"></span></a>AtleastOneSqlMetastoreMustBeProvided

-   **Описание**: Для использования пользовательских настроек для метахранилищ Hive и Oozie следует указать сведения о базе данных SQL Azure по меньшей мере для одного компонента.
-   **Устранение**: Пользователь должен указать допустимое метахранилище SQL Azure и повторить запрос.

### <span id="AzureRegionNotSupported"></span></a>AzureRegionNotSupported

-   **Описание**: Не удалось создать кластер в области *имяВашейОбласти*. Выберите допустимую область HDInsight и повторите запрос.
-   **Устранение**: Клиент должен создать область, которая в настоящее время поддерживает кластеры: Юго-Восточная Азия Западная Европа, Северная Европа, Восток США или Запад США.

### <span id="ClusterContainerRecordNotFound"></span></a>ClusterContainerRecordNotFound

-   **Описание**: Серверу не удалось найти запрошенную запись кластера.
-   **Устранение**: Повторите операцию.

### <span id="ClusterDnsNameInvalidReservedWord"></span></a>ClusterDnsNameInvalidReservedWord

-   **Описание**: Недопустимое DNS-имя кластера *вашеDnsимя*. Убедитесь, что имя начинается и оканчивается цифрой или буквой и не содержит специальных символов отличных от '-'
-   **Устранение**: Убедитесь, что для кластера используется допустимое DNS-имя, которое начинается с цифры или буквы и заканчивается на цифру или букву и при этом не содержит специальных символов кроме дефиса "-", а затем повторите операцию.

### <span id="ClusterNameUnavailable"></span></a>ClusterNameUnavailable

-   **Описание**: Недоступное имя кластера *имяВашегоКластера*. Выберите другое имя.
-   **Устранение**: Пользователь должен указать уникальное имя кластера, которое не существует, а затем повторить попытку. Если пользователь использует портал, в процессе создания кластера ему будет выведено уведомление в пользовательском интерфейсе о том, что это имя кластера уже существует.

### <span id="ClusterPasswordInvalid"></span></a>ClusterPasswordInvalid

-   **Описание**: Недопустимый пароль кластера. Пароль должен быть не короче 10 символов и содержать по крайней мере одну цифру, строчную букву, прописную букву и специальный символ без пробелов, а также не может включать имя пользователя.
-   **Устранение**: Укажите допустимый пароль кластера и повторите операцию.

### <span id="ClusterUserNameInvalid"></span></a>ClusterUserNameInvalid

-   **Описание**: Недопустимое имя пользователя для кластера. Убедитесь, что имя пользователя не содержит специальных символов или пробелов.
-   **Устранение**: Укажите допустимое имя пользователя для кластера и повторите операцию.

### <span id="ClusterUserNameInvalidReservedWord"></span></a>ClusterUserNameInvalidReservedWord

-   **Описание**: Недопустимое DNS-имя кластера *вашеDnsимяКластера*. Убедитесь, что имя начинается и оканчивается цифрой или буквой и не содержит специальных символов отличных от '-'
-   **Устранение**: Укажите допустимое имя пользователя для DNS кластера и повторите операцию.

### <span id="ContainerNameMisMatchWithDnsName"></span></a>ContainerNameMisMatchWithDnsName

-   **Описание**: Имя контейнера в URI *URIВашегоКонтейнера* и DNS-имя *вашеDNSимя* в тексте запроса должны быть одинаковыми.
-   **Устранение**: Убедитесь, что DNS-имя и имя контейнера одинаковы и повторите операцию.

### <span id="DataNodeDefinitionNotFound"></span></a>DataNodeDefinitionNotFound

-   **Описание**: Недопустимая конфигурация кластера. Не удалось найти определения узлов данных в размерах узлов.
-   **Устранение**: Повторите операцию.

### <span id="DeploymentDeletionFailure"></span></a>DeploymentDeletionFailure

-   **Описание**: Ошибка при удалении развертывания для кластера
-   **Устранение**: Повторите операцию удаления.

### <span id="DnsMappingNotFound"></span></a>DnsMappingNotFound

-   **Описание**: Ошибка конфигурации службы. Не удалось найти требуемые сведения о сопоставлении DNS.
-   **Устранение**: Удалите кластер и создайте новый.

### <span id="DuplicateClusterContainerRequest"></span></a>DuplicateClusterContainerRequest

-   **Описание**: Попытка создать повторяющийся контейнер кластера. Для контейнера *имяВашегоКонтейнера* существует запись, но теги eTag не совпадают.
-   **Устранение**: Укажите уникальное имя для контейнера и повторите операцию создания.

### <span id="DuplicateClusterInHostedService"></span></a>DuplicateClusterInHostedService

-   **Описание**: Размещенная служба *имяВашейРазмещеннойСлужбы* уже содержит кластер. Размещенная служба не может содержать несколько кластеров.
-   **Устранение**: Разместите узел кластера в другой размещенной службе.

### <span id="FailureToUpdateDeploymentStatus"></span></a>FailureToUpdateDeploymentStatus

-   **Описание**: Серверу не удалось обновить состояние развертывания кластера.
-   **Устранение**: Повторите операцию. Если ошибка повторится несколько раз, обратитесь в службу поддержки.

### <span id="HdiRestoreClusterAltered"></span></a>HdiRestoreClusterAltered

-   **Описание**: Кластер *имяВашегоКластера* был удален в процессе обслуживания. Создайте кластер снова.
-   **Устранение**: Создайте кластер повторно.

### <span id="HeadNodeConfigNotFound"></span></a>HeadNodeConfigNotFound

-   **Описание**: Недопустимая конфигурация кластера. Требуемые настройки головного узла не найдены в размерах узлов.
-   **Устранение**: Повторите операцию.

### <span id="HostedServiceCreationFailure"></span></a>HostedServiceCreationFailure

-   **Описание**: Не удалось создать размещенную службу *имяВашейРазмещеннойСлужбы*. Повторите запрос.
-   **Устранение**: Повторите запрос.

### <span id="HostedServiceHasProductionDeployment"></span></a>HostedServiceHasProductionDeployment

-   **Описание**: Размещенная служба *имяВашейРазмещеннойСлужбы* уже содержит рабочее развертывание. Размещенная служба не может содержать несколько рабочих развертываний. Повторите запрос с другим именем кластера.
-   **Устранение**: Воспользуйтесь другим именем кластера и повторите запрос.

### <span id="HostedServiceNotFound"></span></a>HostedServiceNotFound

-   **Описание**: Не удалось найти размещенную службу *имяВашейРазмещеннойСлужбы* для кластера.
-   **Устранение**: Если кластер находится в состоянии ошибки, удалите его и повторите попытку.

### <span id="HostedServiceWithNoDeployment"></span></a>HostedServiceWithNoDeployment

-   **Описание**: Размещенная служба *имяВашейРазмещеннойСлужбы* не содержит связанное развертывание.
-   **Устранение**: Если кластер находится в состоянии ошибки, удалите его и повторите попытку.

### <span id="InsufficientResourcesCores"></span></a>InsufficientResourcesCores

-   **Описание**: В подписке с идентификатором *ИДВашейПодписки* не осталось ядер для создания кластера *имяВашегоКластера*. Требуется: *требуемыеРесурсы*, доступно: *доступныеРесурсы*.
-   **Устранение**: Освободите ресурсы в своей подписке или увеличьте количество ресурсов, доступных для подписки, а затем попробуйте создать кластер снова.

### <span id="InsufficientResourcesHostedServices"></span></a>InsufficientResourcesHostedServices

-   **Описание**: В подписке с идентификатором *ИДВашейПодписки* нет квоты на новую размещенную службу для создания кластера *имяВашегоКластера*.
-   **Устранение**: Освободите ресурсы в своей подписке или увеличьте количество ресурсов, доступных для подписки, а затем попробуйте создать кластер снова.

### <span id="InternalErrorRetryRequest"></span></a>InternalErrorRetryRequest

-   **Описание**: Сервер обнаружил внутреннюю ошибку. Повторите запрос.
-   **Устранение**: Повторите запрос.

### <span id="InvalidAzureStorageLocation"></span></a>InvalidAzureStorageLocation

-   **Описание**: Недопустимое расположение хранилища Azure *имяОбластиДанных*. Убедитесь, что регион указан правильно, и повторите попытку.
-   **Устранение**: Выберите расположение хранилища, которое поддерживает HDInsight, убедитесь, что кластер находится в совмещенном расположении, и повторите операцию.

### <span id="InvalidNodeSizeForDataNode"></span></a>InvalidNodeSizeForDataNode

-   **Описание**: Недопустимый размер виртуальной машины для узлов данных. Для всех узлов данных поддерживается только размер 'Большая ВМ'.
-   **Устранение**: Укажите поддерживаемый размер узла данных и повторите операцию.

### <span id="InvalidNodeSizeForHeadNode"></span></a>InvalidNodeSizeForHeadNode

-   **Описание**: Недопустимый размер виртуальной машины для головного узла. Для головного узла поддерживается только размер "Виртуальная машина ExtraLarge".
-   **Устранение**: Укажите поддерживаемый размер узла данных и повторите операцию.

### <span id="InvalidRightsForDeploymentDeletion"></span></a>InvalidRightsForDeploymentDeletion

-   **Описание**: Используемый идентификатор подписки *ИДВашейПодписки* не имеет достаточных разрешений для выполнения операции удаления для кластера *имяВашегоКластера*.
-   **Устранение**: Если кластер находится в состоянии ошибки, удалите его и повторите попытку.

### <span id="InvalidStorageAccountBlobContainerName"></span></a>InvalidStorageAccountBlobContainerName

-   **Описание**: Недопустимое имя контейнера двоичных объектов большого размера для внешней учетной записи хранения *имяВашегоКонтейнера*. Убедитесь, что имя начинается с буквы и содержит только строчные буквы, цифры и дефис.
-   **Устранение**: Укажите допустимое имя учетной записи хранения контейнера двоичных объектов большого размера и повторите операцию.

### <span id="InvalidStorageAccountConfigurationSecretKey"></span></a>InvalidStorageAccountConfigurationSecretKey

-   **Описание**: Для конфигурации внешней учетной записи хранения *имяВашейУчетнойЗаписиХранения* требуется задать данные секретного ключа.
-   **Устранение**: Укажите допустимый секретный ключ для учетной записи хранения и повторите операцию.

### <span id="InvalidVersionHeaderFormat"></span></a>InvalidVersionHeaderFormat

-   **Описание**: Формат заголовка версии *вашЗаголовокВерсии* отличается от допустимого формата гггг-мм-дд.
-   **Устранение**: Укажите допустимый формат для заголовка версии и повторите запрос.

### <span id="MoreThanOneHeadNode"></span></a>MoreThanOneHeadNode

-   **Описание**: Недопустимая конфигурация кластера. Обнаружена конфигурация с несколькими головными узлами.
-   **Устранение**: Измените конфигурацию таким образом, чтобы был указан только один головной узел.

### <span id="OperationTimedOutRetryRequest"></span></a>OperationTimedOutRetryRequest

-   **Описание**: Не удалось завершить операцию в течение допустимого времени или в пределах максимального количества повторных попыток. Повторите запрос.
-   **Устранение**: Повторите запрос.

### <span id="ParameterNullOrEmpty"></span></a>ParameterNullOrEmpty

-   **Описание**: Параметр *имяВашегоПараметра* не может быть неопределенным или пустым.
-   **Устранение**: Укажите допустимое значение для этого параметра.

### <span id="PreClusterCreationValidationFailure"></span></a>PreClusterCreationValidationFailure

-   **Описание**: Недопустимые входные данные для запроса на создание кластера. Убедитесь, что указаны правильные входные значения, и повторите запрос.
-   **Устранение**: Убедитесь, что указаны правильные входные значения, и повторите запрос.

### <span id="RegionCapabilityNotAvailable"></span></a>RegionCapabilityNotAvailable

-   **Описание**: Недоступна поддержка региона *имяВашегоРегиона* и идентификатора подписки *ИДВашейПодписки*.
-   **Устранение**: Укажите регион, который поддерживает кластеры HDInsight. Поддерживаемые регионы: Юго-Восточная Азия Западная Европа, Северная Европа, Восток США или Запад США.

### <span id="StorageAccountNotColocated"></span></a>StorageAccountNotColocated

-   **Описание**: Учетная запись хранения *имяВашейУчетнойЗаписиХранения* находится в регионе *имяТекущегоРегиона*. Она должна совпадать с регионом кластера *имяРегионаВашегоКластера*.
-   **Устранение**: Укажите учетную запись хранения в том же регионе, где находится ваш кластер или, если ваши данные уже находятся в этой учетной записи хранения, создайте новый кластер в регионе существующей учетной записи хранения. Если работа ведется на портале, пользователь заблаговременно получит уведомление об этой проблеме в пользовательском интерфейсе.

### <span id="SubscriptionIdNotActive"></span></a>SubscriptionIdNotActive

-   **Описание**: Указанный идентификатор подписки *ИДВашейПодписки* неактивен.
-   **Устранение**: Повторно активируйте свою подписку или получите новую действующую подписку.

### <span id="SubscriptionIdNotFound"></span></a>SubscriptionIdNotFound

-   **Описание**: Не удалось найти идентификатор подписки *ИДВашейПодписки*.
-   **Устранение**: Убедитесь, что указан допустимый идентификатор подписки, а затем повторите операцию.

### <span id="UnableToResolveDNS"></span></a>UnableToResolveDNS

-   **Описание**: Не удается разрешить DNS *URLВашегоDNS*. Убедитесь, что для конечной точки большого двоичного объекта указан полный URL-адрес.
-   **Устранение**: Укажите допустимый URL-адрес двоичного объекта большого размера. Этот URL-адрес должен быть записан полностью, включая префикс *<http://>* и заканчивая доменом *.com* Полный URL-адрес обычно находится на вкладке хранилища на портале manage.windowsazure.com.

### <span id="UnableToVerifyLocationOfResource"></span></a>UnableToVerifyLocationOfResource

-   **Описание**: Невозможно проверить расположение ресурса *URLВашегоDNS*. Убедитесь, что для конечной точки большого двоичного объекта указан полный URL-адрес.
-   **Устранение**: Укажите допустимый URL-адрес двоичного объекта большого размера. Этот URL-адрес должен быть записан полностью, включая префикс *<http://>* и заканчивая доменом *.com* Полный URL-адрес обычно находится на вкладке хранилища на портале manage.windowsazure.com.

### <span id="VersionCapabilityNotAvailable"></span></a>VersionCapabilityNotAvailable

-   **Описание**: Недоступна поддержка версии *Указанная версия* и идентификатора подписки *ИДВашейПодписки*.
-   **Устранение**: Выберите доступную версию и повторите операцию.

### <span id="VersionNotSupported"></span></a>VersionNotSupported

-   **Описание**: Версия *указаннаяВерсия* не поддерживается.
-   **Устранение**: Выберите поддерживаемую версию и повторите операцию.

### <span id="VersionNotSupportedInRegion"></span></a>VersionNotSupportedInRegion

-   **Описание**: Версия *указаннаяВерсия* недоступна в регионе Azure *указанныйРегион*.
-   **Устранение**: Выберите версию, которая поддерживается в указанном регионе, и повторите операцию.

### <span id="WasbAccountConfigNotFound"></span></a>WasbAccountConfigNotFound

-   **Описание**: Недопустимая конфигурация кластера. Требуемая конфигурация учетной записи WASB не найдена во внешних учетных записях.
-   **Устранение**: Убедитесь, что учетная запись существует и правильно указана в конфигурации, а затем повторите операцию.

## <span id="resources"></span></a>Дополнительные ресурсы для отладки

-   [Документация по пакету Azure HDInsight SDK][Документация по пакету Azure HDInsight SDK]

  [HDI.Debugging.ErrorMessages.Portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png
  [Ошибки HDInsight]: #hdinsight-error-messages
  [Описание и устранение ошибок]: #discription-mitigation-errors
  [AtleastOneSqlMetastoreMustBeProvided]: #AtleastOneSqlMetastoreMustBeProvided
  [AzureRegionNotSupported]: #AzureRegionNotSupported
  [ClusterContainerRecordNotFound]: #ClusterContainerRecordNotFound
  [ClusterDnsNameInvalidReservedWord]: #ClusterDnsNameInvalidReservedWord
  [ClusterNameUnavailable]: #ClusterNameUnavailable
  [ClusterUserNameInvalid]: #ClusterUserNameInvalid
  [ClusterUserNameInvalidReservedWord]: #ClusterUserNameInvalidReservedWord
  [ContainerNameMisMatchWithDnsName]: #ContainerNameMisMatchWithDnsName
  [DataNodeDefinitionNotFound]: #DataNodeDefinitionNotFound
  [DeploymentDeletionFailure]: #DeploymentDeletionFailure
  [DnsMappingNotFound]: #DnsMappingNotFound
  [DuplicateClusterContainerRequest]: #DuplicateClusterContainerRequest
  [DuplicateClusterInHostedService]: #DuplicateClusterInHostedService
  [FailureToUpdateDeploymentStatus]: #FailureToUpdateDeploymentStatus
  [HdiRestoreClusterAltered]: #HdiRestoreClusterAltered
  [HeadNodeConfigNotFound]: #HeadNodeConfigNotFound
  [HostedServiceCreationFailure]: #HostedServiceCreationFailure
  [HostedServiceHasProductionDeployment]: #HostedServiceHasProductionDeployment
  [HostedServiceNotFound]: #HostedServiceNotFound
  [HostedServiceWithNoDeployment]: #HostedServiceWithNoDeployment
  [InsufficientResourcesCores]: #InsufficientResourcesCores
  [InsufficientResourcesHostedServices]: #InsufficientResourcesHostedServices
  [InternalErrorRetryRequest]: #InternalErrorRetryRequest
  [InvalidAzureStorageLocation]: #InvalidAzureStorageLocation
  [InvalidNodeSizeForDataNode]: #InvalidNodeSizeForDataNode
  [InvalidNodeSizeForHeadNode]: #InvalidNodeSizeForHeadNode
  [InvalidRightsForDeploymentDeletion]: #InvalidRightsForDeploymentDeletion
  [InvalidStorageAccountBlobContainerName]: #InvalidStorageAccountBlobContainerName
  [InvalidStorageAccountConfigurationSecretKey]: #InvalidStorageAccountConfigurationSecretKey
  [InvalidVersionHeaderFormat]: #InvalidVersionHeaderFormat
  [MoreThanOneHeadNode]: #MoreThanOneHeadNode
  [OperationTimedOutRetryRequest]: #OperationTimedOutRetryRequest
  [ParameterNullOrEmpty]: #ParameterNullOrEmpty
  [PreClusterCreationValidationFailure]: #PreClusterCreationValidationFailure
  [RegionCapabilityNotAvailable]: #RegionCapabilityNotAvailable
  [StorageAccountNotColocated]: #StorageAccountNotColocated
  [SubscriptionIdNotActive]: #SubscriptionIdNotActive
  [SubscriptionIdNotFound]: #SubscriptionIdNotFound
  [UnableToResolveDNS]: #UnableToResolveDNS
  [UnableToVerifyLocationOfResource]: #UnableToVerifyLocationOfResource
  [VersionCapabilityNotAvailable]: #VersionCapabilityNotAvailable
  [VersionNotSupported]: #VersionNotSupported
  [VersionNotSupportedInRegion]: #VersionNotSupportedInRegion
  [WasbAccountConfigNotFound]: #WasbAccountConfigNotFound
  [Документация по пакету Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/ru-ru/library/dn479185.aspx
