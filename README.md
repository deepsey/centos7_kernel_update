# Обновление ядра до версии 6.0.5 в centos7
## Обновление ядра из репозитория
#### Цели:
- Обновить ядро из репозитория;
- Смонтировать `virtualbox shared folders` при поднятии машины через `Vagrant`.
#### Исходные данные:
- Виртуальная лаборатория на `Vagrant`:
  - **VirtualBox** - среда виртуализации, позволяет создавать и выполнять виртуальные машины;
  - **Vagrant** - ПО для создания и конфигурирования виртуальной среды. В данном случае в качестве среды виртуализации используется `VirtualBox`;
  - **Git** - система контроля версий
- Данный репозиторий:
  - `Vagrantfile` - файл описывающий виртуальную инфраструктуру для `Vagrant`.   
---
#### Устанавливаем свежеее ядро из репозитория

    sudo yum install -y http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
    sudo yum --enablerepo elrepo-kernel install kernel-ml -y
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    sudo grub2-set-default 0
    
#### Перезагружаем машину, смотрим вывод загрузки    
    vagrant reload

---

    VirtualBox Guest Additions: Building the VirtualBox Guest Additions kernel 
    modules.  This may take a while.   
    VirtualBox Guest Additions: To build modules for other installed kernels, run  
    VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup <version>  
    VirtualBox Guest Additions: or  
    VirtualBox Guest Additions:   /sbin/rcvboxadd quicksetup all  
    VirtualBox Guest Additions: Kernel headers not found for target kernel   
    6.0.4-1.el7.elrepo.x86_64. Please install them and execute  
    /sbin/rcvboxadd setup  
    modprobe vboxguest failed  
    The log file /var/log/vboxadd-setup.log may contain further information.  
---
Видим, что не устанавливаются kernel modules. Нужны kernel headers для нового ядра, а также в логах ругань на версию gcc, которая должна быть 9.3.0.

#### Установим Kernel headers для ядра 6.0.4-1.el7.elrepo.x86_64

    sudo yum remove kernel-headers
    sudo yum --enablerepo elrepo-kernel install kernel-ml-headers.x86_64 kernel-ml-devel.x86_64    
    
#### Установим gcc версии 9.3.0 из исходников

    sudo yum -y install bzip2 wget gcc gcc-c++ gmp-devel mpfr-devel libmpc-devel make
    mkdir gcc
    cd gcc/
    wget http://mirror.linux-ia64.org/gnu/gcc/releases/gcc-9.3.0/gcc-9.3.0.tar.gz
    tar -xvf gcc-9.3.0.tar.gz
    cd gcc-9.3.0/
    ./configure --enable-languages=c,c++ --disable-multilib
    make -j12
    sudo make install
    gcc --version
      gcc (GCC) 9.3.0  
      
#### Перезагружаем машину через vagrant, видим корректное монтирование vbox shared folders      

    vagrant reload  
    
    ==> centos7-002: Mounting shared folders...  
    centos7-002: /vagrant => /mnt/lvm_4/vagrant/centos7-002  
    ==> centos7-002: Machine already provisioned. Run `vagrant provision` or use the `--provision`  
    ==> centos7-002: flag to force provisioning. Provisioners marked to run always will still run.  
    
#### Done!    

