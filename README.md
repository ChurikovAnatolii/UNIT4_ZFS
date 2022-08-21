
# UNIT4_ZFS


### 1. Определить алгоритм с наилучшим сжатием.

-  #### определить какие алгоритмы сжатия поддерживает zfs

> **lz4** - алгоритм сжатия данных без потерь, ориентированный на высокую скорость сжатия и распаковки. Используется в ZFS по-умолчанию.  
> **zstd** - это быстрый алгоритм сжатия, обеспечивающий высокую степень сжатия. Он также предлагает специальный режим для небольших   >данных, называемый сжатием с использованием словаря;  
> **gzip** - проверенный временем метод сжатия данных, разрабатываемый с 1992 года и использующий алгоритм deflate;  
> **zle** - это кодирование нулевого уровня - оно не сжимает обычные данные, но сжимает большие последовательности нулей. Полезно для   >полностью несжимаемых наборов данных (например, JPEG, MP4 или других уже сжатых форматов), поскольку он игнорирует несжимаемые   >данные, но сжимает свободное пространство в окончательных записях;  
> **lzjb** - алгоритм сжатия данных без потерь, изобретённый в 1998 году для сжатия аварийных дампов программ и данных в файловой системе ZFS. Ранее использовался в ZFS по-умолчанию, но сейчас уступил место более быстрому и эффективному lz4.

- #### создать 4 файловых системы на каждой применить свой алгоритм сжатия 

-***Для начала cоздадим zfs pool***  
> 
> *create -o ashift=12 unit4 raidz1 sd{b,c,d}*  
>
> *zpool status*  
>
>  NAME        STATE     READ WRITE CKSUM  
        unit4       ONLINE       0     0     0  
          raidz1-0  ONLINE       0     0     0  
            sdb     ONLINE       0     0     0  
            sdc     ONLINE       0     0     0  
            sdd     ONLINE       0     0     0 
            
-***Создадим файловые системы, установим, квоты и компрессию***  

> zfs create unit4/filesystem1  
> zfs create unit4/filesystem2  
> zfs create unit4/filesystem3  
> zfs create unit4/filesystem4  
>
> zfs set quota=500M unit4/filesystem1  
> zfs set quota=500M unit4/filesystem2  
> zfs set quota=500M unit4/filesystem3  
> zfs set quota=500M unit4/filesystem4
>
> zfs set compression=zstd unit4/filesystem1    
> zfs set compression=gzip unit4/filesystem2  
> zfs set compression=zle unit4/filesystem3   
> zfs set compression=lzjb unit4/filesystem4  

-***Скачиваем файлик, раскидываем по ФС, смотрим где сколько места осталось***  

> wget -O war_and_peace.txt http://www.gutenberg.org/ebooks/2600.txt.utf-8   
> cp -r war_and_peace.txt /unit4/filesystem[1-4]  
> zfs list  
>  NAME                USED  AVAIL     REFER  MOUNTPOINT  
>  unit4              20.1M  1.69G     3.42M  /unit4  
>  unit4/filesystem1  1.46M   499M     1.46M  /unit4/filesystem1  
>  unit4/filesystem2  1.42M   499M     1.42M  /unit4/filesystem2  
>  unit4/filesystem3  3.34M   497M     3.34M  /unit4/filesystem3  
>  unit4/filesystem4  9.90M   490M     9.90M  /unit4/filesystem4  

-***Вывод: наименьтший размер занятый файлом на файловой системе 2, где применен метод сжатия gzip***
---
### 2. Определить настройки pool’a. 

-***Скачиваем архив, распаковываем, восстанавливаем пул***

>  wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1KRBNW33QWqbvbVHa3hLJivOAt60yukkg' -O arch.tar.gz  
> tar -xzvf arch.tar.gz

>  zpool import -d zpoolexport  
>  otus                                 ONLINE  
>         mirror-0                           ONLINE  
>           /home/vagrant/zpoolexport/filea  ONLINE  
>           /home/vagrant/zpoolexport/fileb  ONLINE
>
>  zpool import -d zpoolexport otus

-***Определяем настройки пула***

> zfs get all otus/hometask2 >> settings_hometask2.txt -__Записываем настройки пула otus в файл__ [параметров](https://github.com/ChurikovAnatolii/UNIT4_ZFS/blob/main/settings_otus.txt)  
>  zfs get all otus/hometask2 >> settings_hometask2.txt -__Записываем настройки пула otus/hometask2 в файл__ [параметров](https://github.com/ChurikovAnatolii/UNIT4_ZFS/blob/main/settings_hometask2.txt)









