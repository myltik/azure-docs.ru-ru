1. Установите dapl, rdmacm, ibverbs и mlx4.

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. В /etc/waagent.conf включите RDMA, раскомментировав следующие строки конфигурации. Для изменения этого файла требуется доступ с правами root.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Добавьте или измените следующие параметры памяти в КБ в файле /etc/security/limits.conf. Для изменения этого файла требуется доступ с правами root. В целях тестирования можно также задать неограниченное значение для параметра memlock. Например, `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Установите библиотеку Intel MPI [Купите и скачайте](https://software.intel.com/intel-mpi-library/) библиотеку с сайта Intel или скачайте [бесплатную пробную версию](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
 wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/11595/l_mpi_2017.3.196.tgz
   ```
 
 Шаги установки см. в [руководстве по установке библиотеки Intel MPI](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Включите ptrace для некорневых процессов без отладчика (требуется для последних версий Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
