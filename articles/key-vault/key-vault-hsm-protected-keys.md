---
title: Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure | Microsoft Docs
description: Данная статья поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure.
services: key-vault
documentationcenter: ''
author: cabailey
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cabailey

---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure
## <a name="introduction"></a>Введение
Чтобы обеспечить более высокий уровень защиты при работе с хранилищем ключей Azure, можно импортировать ключи или создать их в аппаратных модулях безопасности (ключи никогда не покидают их пределы). Сценарий с использованием собственного ключа клиента называется *BYOK*. В качестве аппаратных модулей безопасности используются модули FIPS 140-2 с проверкой уровня 2. Хранилище ключей Azure защищает ваши ключи с помощью семейства аппаратных модулей безопасности Thales nShield.

Информация в этой статье поможет вам подготовить и создать ваши собственные ключи, защищенные аппаратным модулем безопасности, а затем передать их в хранилище ключей Azure.

Эта функция недоступна для Китая. 

> [!NOTE]
> Дополнительные сведения о хранилище ключей Azure см. в статье [Что такое хранилище ключей Azure?](key-vault-whatis.md)  
> 
> Указания по началу работы, включая создание хранилища для ключей, защищенных аппаратным модулем безопасности, см. в статье [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md).
> 
> 

Дополнительные сведения о создании и передаче ключей, защищенных аппаратным модулем безопасности, через Интернет:

* Ключ создается на автономной рабочей станции, что позволяет уменьшить область атаки.
* Ключ шифруется с использованием ключа обмена ключами и остается зашифрованным до тех пор, пока не будет передан в аппаратные модули безопасности хранилища ключей Azure. Исходную рабочую станцию покидает только зашифрованная версия ключа.
* Набор инструментов задает свойства ключа клиента, который привязывает ваш ключ к системе безопасности хранилища ключей Azure. В связи с этим после, как только аппаратные модули безопасности хранилища ключей Azure получат и расшифруют ваш ключ, использовать его смогут только они. Экспортировать ключ нельзя. Эта привязка реализуется аппаратными модулями безопасности Thales.
* Ключ обмена ключами, используемый для шифрования вашего ключа, создается в аппаратных модулях безопасности хранилища ключей Azure и не может быть экспортирован. Аппаратные модули безопасности не допускают существования ключа обмена ключами в чистом виде за пределами аппаратных модулей безопасности. Кроме того, этот набор инструментов включает аттестацию, в процессе которой Thales проверяет, можно ли экспортировать ключ обмена ключами и был ли он создан в подлинном аппаратном модуле безопасности производства Thales.
* Набор инструментов включает аттестацию, в процессе которой Thales проверяет, была ли система безопасности хранилища ключей Azure создана в подлинном аппаратном модуле безопасности производства Thales. Эта аттестация является доказательством того, что корпорация Майкрософт использует подлинное оборудование.
* Корпорация Майкрософт применяет отдельные ключи шифрования ключей, а также отдельные системы безопасности в каждом географическом регионе. Такое разделение значит, что ваш ключ может использоваться только в центрах обработки данных того региона, в котором он был зашифрован. Например, ключ европейского клиента нельзя использовать в центрах обработки данных в Северной Америке или Азии.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Дополнительные сведения об аппаратных модулях безопасности Thales и службах Майкрософт
Thales e-Security — ведущий международный поставщик решений для шифрования данных и защиты от киберпреступности в сфере финансовых услуг, высоких технологий, производства, государственного сектора и технологий. Решения Thales, созданные на основе 40-летнего опыта защиты корпоративной и государственной информации, используются в четырех из пяти крупнейших энергетических и аэрокосмических компаний. Эти решения также используются в 22 странах НАТО и защищают более 80 процентов платежных транзакций по всему миру.

Сотрудничество с компанией Thales позволяет корпорации Майкрософт повышать уровень технического развития аппаратных модулей безопасности. Эти улучшения позволят вам пользоваться стандартными преимуществами размещаемых служб, не теряя контроль над ключами, а корпорации Майкрософт — управлять аппаратными модулями безопасности за вас. Поскольку хранилище ключей Azure является облачной службой, она легко масштабируется в соответствии с потребностями вашей организации. В то же время ваш ключ защищен аппаратными модулями безопасности Майкрософт: вы получаете контроль над жизненным циклом ключа, так как создаете ключ и перемещаете его в аппаратные модули безопасности Майкрософт.

## <a name="implementing-bring-your-own-key-(byok)-for-azure-key-vault"></a>Реализация сценария BYOK для хранилища ключей Azure
Используйте сведения и процедуры этого раздела при создании собственного ключа, защищенного аппаратным модулем безопасности, и передачи этого ключа в хранилище ключей Azure — сценарий BYOK.

## <a name="prerequisites-for-byok"></a>Предварительные требования для BYOK
Список предварительных требований для реализации сценария BYOK для хранилища ключей Azure к см. в приведенной ниже таблице.

| Требование | Дополнительные сведения |
| --- | --- |
| Подписка на Azure |Для создания хранилища ключей Azure требуется подписка Azure: [зарегистрируйтесь для получения бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/) |
| Хранилище ключей Azure, поддерживающее аппаратные модули безопасности |Дополнительные сведения об уровнях обслуживания и возможностях хранилища ключей Azure см. на веб-сайте [Цены на хранилище ключей Azure](https://azure.microsoft.com/pricing/details/key-vault/). |
| Аппаратный модуль безопасности Thales, смарт-карты и программное обеспечение поддержки |Вы должны иметь доступ к аппаратному модулю безопасности Thales и базовые знания о работе аппаратных модулей безопасности Thales. Список совместимых моделей или сведения о покупке аппаратного модуля безопасности, если у вас его нет, см. в статье [Аппаратный модуль безопасности Thales](https://www.thales-esecurity.com/msrms/buy). |
| Следующее оборудование и программное обеспечение:<ol><li>Автономная рабочая станция x64 с операционной системой Windows версии не ниже Windows 7 и программным обеспечением Thales nShield версии не ниже 11.50.<br/><br/>Если рабочая станция выполняется на ОС Windows 7, [установите Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Рабочая станция, подключенная к Интернету, с операционной системой Windows версии не ниже Windows 7.</li><li>USB-накопитель или другое переносное устройство хранения, на котором имеется не менее 16 МБ свободного места.</li></ol> |По соображениям безопасности рекомендуется не подключать первую рабочую станцию к сети. Однако это не применяется принудительно программным путем.<br/><br/>Обратите внимание, что далее эта рабочая станция называется отключенной рабочей станцией.</p></blockquote><br/>Кроме того, если ключ клиента предназначен для производственной сети, рекомендуется использовать вторую, отдельную рабочую станцию для скачивания набора средств и отправки ключа клиента. Но в целях тестирования можно использовать первую рабочую станцию.<br/><br/>Обратите внимание, что далее вторая рабочая станция называется рабочей станцией, подключенной к Интернету.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Создание и передача ключа в аппаратный модуль безопасности хранилища ключей Azure
Для создания и передачи ключа в аппаратный модуль безопасности хранилища ключей Azure используются следующие пять шагов:

* [Шаг 1. Подготовка рабочей станции, подключенной к Интернету](#step-1-prepare-your-internet-connected-workstation)
* [Шаг 2. Подготовка отключенной рабочей станции](#step-2-prepare-your-disconnected-workstation)
* [Шаг 3. Создание ключа](#step-3-generate-your-key)
* [Шаг 4. Подготовка ключа к передаче](#step-4-prepare-your-key-for-transfer)
* [Шаг 5. Передача ключа в хранилище ключей Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1:-prepare-your-internet-connected-workstation"></a>Шаг 1. Подготовка рабочей станции, подключенной к Интернету
Чтобы подготовить рабочую станцию, подключенную к Интернету, выполните описанные ниже действия.

### <a name="step-1.1:-install-azure-powershell"></a>Шаг 1.1. Установка Azure PowerShell
С компьютера, подключенного к Интернету, загрузите и установите модуль Azure PowerShell, который содержит командлеты для управления хранилищем ключей Azure. Требуется версия не ниже 0.8.13.

Инструкции по установке см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

### <a name="step-1.2:-get-your-azure-subscription-id"></a>Шаг 1.2. Получение идентификатора подписки Azure
Запустите сеанс Azure PowerShell и выполните вход в учетную запись Azure с помощью следующей команды:

        Add-AzureAccount
Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Затем воспользуйтесь командой [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

        Get-AzureSubscription
В выходных данных найдите идентификатор для подписки, которая будет использоваться для хранилища ключей Azure. Этот идентификатор подписки понадобится позже.

Не закрывайте окно Azure PowerShell.

### <a name="step-1.3:-download-the-byok-toolset-for-azure-key-vault"></a>Шаг 1.3. Загрузка набора инструментов BYOK для хранилища ключей Azure
Перейдите в Центр загрузки Майкрософт и [скачайте набор инструментов BYOK для хранилища ключей Azure](http://www.microsoft.com/download/details.aspx?id=45345) для своего географического региона или экземпляра Azure. Чтобы определить имя пакета для скачивания и его хэш SHA-256, используйте следующие сведения.

- - -
**Северная Америка**

KeyVault-BYOK-Tools-UnitedStates.zip

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

- - -
**Европа**

KeyVault-BYOK-Tools-Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

- - -
**Азия**

KeyVault-BYOK-Tools-AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

- - -
**Латинская Америка**

KeyVault-BYOK-Tools-LatinAmerica.zip

9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

- - -
**Япония**

KeyVault-BYOK-Tools-Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

- - -
**Австралия**

KeyVault-BYOK-Tools-Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

- - -
[**Azure для государственных организаций:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

- - -
**Канада**

KeyVault-BYOK-Tools-Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

- - -
**Германия**

KeyVault-BYOK-Tools-Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

- - -
**Индия**

KeyVault-BYOK-Tools-India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

- - -
Чтобы проверить целостность скачанного набора инструментов BYOK, в сеансе Azure PowerShell запустите командлет [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

    Get-FileHash KeyVault-BYOK-Tools-*.zip

В набор инструментов входит следующее.

* Пакет ключей обмена ключами с именем, начинающимся с **BYOK-KEK-pkg-.**
* Пакет Security World с именем, начинающимся с **BYOK-SecurityWorld-pkg-**
* Сценарий на Python с именем v**erifykeypackage.py.**
* Исполняемый файл командной строки с именем **KeyTransferRemote.exe** и связанными библиотеками DLL.
* Распространяемый пакет Visual C++ с именем **vcredist_x64.exe.**

Скопируйте пакет на USB-накопитель или другое переносное устройство.

## <a name="step-2:-prepare-your-disconnected-workstation"></a>Шаг 2. Подготовка отключенной рабочей станции
Чтобы подготовить рабочую станцию, не подключенную к сети (к Интернету или внутренней сети), выполните указанные ниже действия.

### <a name="step-2.1:-prepare-the-disconnected-workstation-with-thales-hsm"></a>Шаг 2.1. Подготовка отключенной рабочей станции с помощью аппаратного модуля безопасности Thales
Установите программное обеспечение поддержки nCipher (Thales) на компьютере Windows, а затем подключите к этому компьютеру аппаратный модуль безопасности Thales.

Убедитесь, что инструменты Thales расположены в каталоге по вашему пути (**%nfast_home%\bin** и **%nfast_home%\python\bin**). Например, введите следующую команду:

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Дополнительные сведения см. в руководстве пользователя, которое поставляется вместе с аппаратным модулем безопасности Thales.

### <a name="step-2.2:-install-the-byok-toolset-on-the-disconnected-workstation"></a>Шаг 2.2. Установка набора инструментов BYOK на отключенную рабочую станцию
Скопируйте пакет набора инструментов BYOK с USB-накопителя или другого переносного устройства, а затем выполните указанные ниже действия.

1. Извлеките файлы из загруженного пакета в любую папку.
2. Запустите файл vcredist_x64.exe из этой папки.
3. Следуйте инструкциям по установке компонентов среды выполнения Visual C++ для Visual Studio 2013.

## <a name="step-3:-generate-your-key"></a>Шаг 3. Создание ключа
Чтобы создать свой ключ, выполните на отключенной рабочей станции указанные ниже действия.

### <a name="step-3.1:-create-a-security-world"></a>Шаг 3.1. Создание системы безопасности
Откройте командную строку и запустите новую программу системы безопасности Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Эта программа создает файл **Security World** в каталоге %NFAST_KMDATA%\local\world, который соответствует папке C:\ProgramData\nCipher\Key Management Data\local. Можно использовать разные значения для кворума, но в данном примере вам будет предложено ввести три пустые карты и закрепления для каждой из них. После этого любые две карты предоставят полный доступ к системе безопасности. Эти карты станут **набором карт администратора** для новой системы безопасности.

После этого выполните описанные ниже действия.

* Создайте резервную копию файла системы безопасности. Защитите файл системы безопасности, карты администратора и их закрепления и убедитесь, что все имеют доступ не больше, чем к одной карте.

### <a name="step-3.2:-validate-the-downloaded-package"></a>Шаг 3.2. Проверка загруженного пакета
Этот шаг необязателен, но рекомендуется, поскольку позволяет проверить следующее.

* Ключ обмена ключами, включенный в набор инструментов, создан в подлинном аппаратном модуле безопасности Thales.
* Хэш системы безопасности, включенный в набор инструментов, создан в подлинном аппаратном модуле безопасности Thales.
* Ключ обмена ключами недоступен для экспорта.

> [!NOTE]
> Для проверки загруженного пакета аппаратный модуль безопасности должен быть подключен, включен и оснащен системой безопасности (которую вы только что создали).
> 
> 

Проверка загруженного пакета

1. Запустите сценарий verifykeypackage.py, указав одно из приведенных ниже значений в зависимости от вашего географического региона или экземпляра:
   
   * Для Северной Америки:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Для Европы:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Для Азии:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Для Латинской Америки:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Для Японии:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Для Австралии:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Для [Azure для государственных организаций](https://azure.microsoft.com/features/gov/), использующего экземпляр Azure для правительства США:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Для Канады:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Для Германии:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Для Индии:
     
           python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
     > [!TIP]
     > Программное обеспечение Thales включает файл python, который находится в каталоге в %NFAST_HOME%\python\bin.
     > 
     > 
2. Должен появиться следующий текст, показывающий, что проверка пройдена: **Результат: УСПЕШНО**

Этот сценарий проверяет цепочку до корневого ключа Thales подписавшего пользователя. Хэш этого корневого ключа встроен в сценарий и должен иметь значение **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Кроме того, вы можете проверить это значение отдельно на [веб-сайте Thales](http://www.thalesesec.com/).

Теперь можно создать новый ключ.

### <a name="step-3.3:-create-a-new-key"></a>Шаг 3.3. Создание ключа
Создайте ключ с помощью программы **generatekey** в Thales.

Для создания ключа выполните следующую команду:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

При выполнении команды следуйте приведенным ниже инструкциям.

* Параметру *protect* должно быть задано значение **module**, как показано. В результате будет создан ключ, защищенный модулем. Набор средств BYOK не поддерживает ключи, защищенные OCS.
* Замените значение *contosokey* для параметров **ident** и **plainname** любым строковым значением. Чтобы минимизировать административные затраты и снизить риск возникновения ошибок, рекомендуется использовать для обоих параметров одно и то же значение. Значение параметра **Ident** должно содержать только цифры, тире и буквы нижнего регистра.
* В данном примере значение параметра pubexp остается пустым (по умолчанию), но можно указать и конкретные значения. Дополнительные сведения см. в документации Thales.

Эта команда создает файл ключа с маркером в папке %NFAST_KMDATA%\local с именем, начинающимся с **key_simple_**, за которым следует значение параметра **ident**, указанное в команде. Пример: **key_simple_contosokey**. Этот файл содержит зашифрованный ключ.

Создайте резервную копию файла ключа с токеном в безопасном расположении.

> [!IMPORTANT]
> После передачи ключа в хранилище ключей Azure корпорация Майкрософт не сможет экспортировать ключ обратно, поэтому крайне важно сделать резервную копию ключа и системы безопасности. За инструкциями и рекомендациями по резервному копированию ключа обращайтесь в компанию Thales.
> 
> 

После этого ключ можно передать в хранилище ключей Azure.

## <a name="step-4:-prepare-your-key-for-transfer"></a>Шаг 4. Подготовка ключа к передаче
Чтобы подготовить ключ к передаче, выполните на отключенной рабочей станции указанные ниже действия.

### <a name="step-4.1:-create-a-copy-of-your-key-with-reduced-permissions"></a>Шаг 4.1. Создание копии ключа с ограниченными разрешениями
Чтобы ограничить разрешения в ключе, выполните через командную строку одну из следующих команд в зависимости от вашего географического региона или экземпляра Azure.

* Для Северной Америки:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Для Европы:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Для Азии:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Для Латинской Америки:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Для Японии:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Для Австралии:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Для [Azure для государственных организаций](https://azure.microsoft.com/features/gov/), использующего экземпляр Azure для правительства США:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Для Канады:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Для Германии:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Для Индии:
  
        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1

При запуске этой команды замените *contosokey* тем же значением, которое вы указали при выполнении этапа **Шаг 3.3. Создание ключа** процедуры [Создание ключа](#step-3-generate-your-key) .

Появится запрос на подключение карт администратора системы безопасности.

После завершения команды появится текст **Результат: успешно<contosokey>, а копия ключа с ограниченными разрешениями появится в файле с именем key_xferacId_**.

### <a name="step-4.2:-inspect-the-new-copy-of-the-key"></a>Шаг 4.2. Проверка новой копии ключа
При необходимости запустите служебные программы Thales, чтобы подтвердить минимальные разрешения в новом ключе:

* aclprint.py:
  
        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:
  
        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  При запуске этих команд замените contosokey тем же значением, которое вы указали на этапе **Шаг 3.3. Создание ключа** в разделе [Создание ключа](#step-3-generate-your-key).

### <a name="step-4.3:-encrypt-your-key-by-using-microsoft’s-key-exchange-key"></a>Шаг 4.3. Шифрование ключа с помощью ключа обмена ключами Майкрософт
Выполните одну из следующих команд в зависимости от вашего географического региона или экземпляра Azure:

* Для Северной Америки:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Европы:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Азии:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Латинской Америки:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Японии:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Австралии:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для [Azure для государственных организаций](https://azure.microsoft.com/features/gov/), использующего экземпляр Azure для правительства США:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Канады:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Германии:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Для Индии:
  
        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

При выполнении команды следуйте приведенным ниже инструкциям.

* Замените *contosokey* идентификатором, который вы использовали для создания ключа на этапе **Шаг 3.3. Создание ключа** в разделе [Создание ключа](#step-3-generate-your-key) .
* Замените *SubscriptionID* на идентификатор подписки Azure, содержащей ваше хранилище ключей. Это значение было получено ранее на этапе **Шаг 1.2. Получение идентификатора подписки Azure** процедуры [Подготовка рабочей станции, подключенной к Интернету](#step-1-prepare-your-internet-connected-workstation) .
* Замените *ContosoFirstHSMKey* меткой, которая используется для имени выходного файла.

После выполнения команды отобразится сообщение **Результат: успешно**, а в текущей папке появится новый файл с именем TransferPackage-*ContosoFirstHSMkey*.byok.

### <a name="step-4.4:-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Шаг 4.4. Копирование пакета передачи ключа на рабочую станцию, подключенную к Интернету
С помощью USB-накопителя или другого переносного устройства скопируйте выходной файл из предыдущего шага (KeyTransferPackage-ContosoFirstHSMkey.byok) на рабочую станцию, подключенную к Интернету.

## <a name="step-5:-transfer-your-key-to-azure-key-vault"></a>Шаг 5. Передача ключа в хранилище ключей Azure
Наконец, на рабочей станции, подключенной к Интернету, запустите командлет [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx).aspx), чтобы отправить пакет передачи ключа, скопированный с отключенной рабочей станции, в аппаратный модуль безопасности хранилища ключей Azure:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

При успешном завершении отправки на экране появятся свойства добавленного ключа.

## <a name="next-steps"></a>Дальнейшие действия
Теперь ключ, защищенный с помощью аппаратного модуля безопасности, можно использовать в хранилище ключей. Дополнительные сведения см. в разделе **Использование аппаратного модуля безопасности** руководства [Приступая к работе с хранилищем ключей Azure](key-vault-get-started.md).

<!--HONumber=Oct16_HO2-->


