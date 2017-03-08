| **Оборудование** | |
| --- |---|
| Количество ядер ЦП| 8 |
| ОЗУ| 12 ГБ|
| Количество дисков | 3 <br><br> — диск ОС;<br> — диск кэша сервера обработки;<br> — диск хранения (для восстановления размещения).|
| Свободное место на диске (кэш сервера обработки) | 600 ГБ
| Свободное место на диске (диск хранения) | 600 ГБ|
| **Программное обеспечение** | |
| Версия операционной системы | Windows Server 2012 R2 |
| Язык операционной системы | Английский (en-us)|
| Версия VMware vSphere PowerCLI | [PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0 "PowerCLI 6.0")|
| Роли Windows Server | Не включайте такие роли, как: <br> — доменные службы Active Directory; <br>— службы IIS; <br> — Hyper-V. |
| **Сеть** | |
| Тип карты сетевого интерфейса | VMXNET3 |
| Тип IP-адреса | Статическое |
| Доступ к Интернету | Сервер должен иметь доступ к следующим URL-адресам (напрямую или через прокси-сервер): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (не требуется для сервера обработки масштабирования) <br> time.nist.gov <br> time.windows.com |
| порты; | 443 (оркестрация канала управления)<br>9443 (передача данных)|
