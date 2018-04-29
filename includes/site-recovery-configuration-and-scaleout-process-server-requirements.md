| **Компонент** | **Требование** |
| --- |---|
| Ядра ЦП| 8 |
| ОЗУ | 16 ГБ|
| Количество дисков | 3 (диск ОС, диск кэша сервера обработки, диск хранения (для восстановления размещения)) |
| Свободное место на диске (кэш сервера обработки) | 600 ГБ
| Свободное место на диске (диск хранения) | 600 ГБ|
| Операционная система  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Язык операционной системы | Английский (en-us)|
| Версия VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Роли Windows Server | Не включайте эти роли: <br> — доменные службы Active Directory; <br>— службы IIS; <br> — Hyper-V. |
| Групповые политики| Не включать эти групповые политики: <br> — запрет на использование командной строки; <br> — запрет на использование инструментов редактирования реестра; <br> — логика доверия для вложенных файлов; <br> — включение выполнения скриптов. <br> [Подробнее](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | — должен отсутствовать предварительно созданный веб-сайт по умолчанию; <br> — включите [анонимную аутентификацию](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx); <br> — включите параметр [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx);  <br> — должен отсутствовать предварительно созданный веб-сайт или приложение, ожидающее передачи данных через порт 443.<br>|
| Тип сетевой карты | VMXNET3 (при развертывании в качестве виртуальной машины VMware) |
| Тип IP-адреса | Статическое |
| Доступ к Интернету | Сервер должен иметь доступ к этим URL-адресам: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> — https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (не требуется для сервера обработки масштабирования) <br> time.nist.gov <br> time.windows.com |
| порты; | 443 (оркестрация канала управления)<br>9443 (передача данных)|
