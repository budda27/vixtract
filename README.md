# [ViXtract](https://www.vixtract.ru)
 
Удобный ETL инструмент с открытым исходным кодом на основе Python.

ViXtract – это сборка на основе популярных открытых инструментов обработки данных, которая помогает аналитикам BI самостоятельно выгружать, очищать и преобразовывать данные без помощи ETL разработчиков. Главные принципы ViXtract – удобство работы аналитика и неограниченные возможности развития. В основе ViXtract лежат три ключевых компонента: Jupyter - интерактивная среда для работы с Python, PETL – простая в освоении библиотека преобразования данных, Cronicle - планировщик с функциями мониторинга и оркестрации задач.

Сайт проекта - https://www.vixtract.ru

## Установка

Для установки ViXtract используйте команду ниже (рекомендуется использовать Ubuntu 18.04 LTS):

```
sudo apt-get update &&\ 
sudo apt-get install git -y &&\
git clone https://github.com/visiologyofficial/vixtract &&\
cd vixtract &&\
sudo chmod +x *.sh &&\
sudo ./install.sh
```

Если планируется использовать ViXtract с публичным доменом и HTTPS, рекомендуется до установки ViXtract настроить домен на DNS-сервере. В этом случае при установке ViXtract можно будет сразу настроить получение SSL сертификата через Letsencypt. 

Установщик запросит следующую информацию:
1. Домен/hostname. Можно оставить пустым для доступа к серверу по IP, но в этом случае автоматическая настройка HTTPS будет недоступна.
2. Согласие на автоматическую настройку HTTPS
3. Имя пользователя и пароль для первой учетной записи. С помощью этой учетной записи можно будет сразу приступить к работе с ViXtract.
3. Пароль для учетной записи администратора Cronicle ('admin')
4. Пароль для учетной записи 'etl' в предустановленной PostgreSQL

После установки ViXtract будет доступен в браузере по адресу http(s)://\<домен или IP\>

При необходимости можно запускать скрипт install.sh многократно.

## Начало работы

Рекомендуем начать знакомство с ViXtract в следующем порядке:
1. Посмотрите вводный ролик по ViXtract в разделе документация на сайте https://www.vixtract.ru
2. Установите ViXtract или зарегистрируйтесь на публичном тестовом сервере
2. Пройдите вводные уроки по ViXtract в формате Jupyter Notebook - папка 'tutorials'
2. Ознакомьтесь со вводным курсом по Python, например "Python за 15 минут" https://www.youtube.com/watch?v=h8ajN8_XiBk
5. Возьмите свою реальную задачу и попробуйте ее реализовать. При этом вам будут полезны:
   1. Примеры реализации коннекторов - папка 'examples'
   2. Чат сообщества ViXtract - https://t.me/vixtract_ru
   3. Документация PETL - https://petl.readthedocs.io/en/stable/

## Настройка ViXtract

Для настройки ViXtract после установки предусмотрен скрипт 'conf.sh'. Доступны следующие ключи:

'-u'
Добавление учетной записи

'-ssl'
Включение/выключение HTTPS

'-h'
Настройка домена/hostname

'-s'
Настройка монтирования облачного хранилища по протоколу S3

'-p'
Настройка пароля учетной записи 'etl' в PostgreSQL

## Технические подробности состава сборки

При установке выполняются следующие действия:
1. Установлен JupyterHub с интерфейсом JupyterLab
2. Установлен менеджер окружений Anaconda
3. Созданы два окружения - dev и prod, а также настроены соответствующие ядра в Jupyter
4. В оба окружения установлен базовый набор пакетов, включая PETL и вспомогательные библиотек
5. Установлен планировшик Cronicle и модуль исполнения papermill
6. Установлен Nginx
7. Установлен S3FS для монтирования облачного хранилища по протоколу S3
7. Установлен и настроен PostgreSQL
8. Создана база etl в PostgreSQL
9. Сделаны дополнительные настройки, интегрирующие все компоненты сборки, в том числе настройки прав, systemd, nginx и др.

## Известные проблемы

1. В текущей версии ViXtract не поддерживает установку в окружении за корпоративным прокси с авторизацией. В таком случае необходимо до установки вручную прописать конфигурацию прокси для npm, anaconda, wget, причем для wget также может потребоваться отключение проверки сертификата 'check_certificate = off'
2. Что то с локалью, возможно lxc

```
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
```
>>> Исправляем на этапе перед установкой:

```
dpkg-reconfigure locales
# И отметить нужное!
en_US.UTF-8
ru_RU.UTF-8
# После дефолтную делаем en_US.UTF-8
```



## Установка в LXC
### Opensuse:

* Устанавливаем свежий пакет debootstrap
```
https://software.opensuse.org/package/debootstrap
```
```
zypper ar http://download.opensuse.org/distribution/leap/15.2/repo/oss/ debootstrap
zypper in debootstrap
```

* Готовим окружение Ubuntu 18 LTS (Bionic)

mkdir -p /var/lib/lxc/vixtract
cd /var/lib/lxc/vixtract/
```
debootstrap --arch=amd64 bionic ./ROOTFS http://archive.ubuntu.com/ubuntu/
```

* Задаём пароль:

```
chroot test/
```

```
vi /etc/apt/sources.list
# Добавляем:
deb http://archive.ubuntu.com/ubuntu bionic main universe
deb http://archive.ubuntu.com/ubuntu bionic-security main universe 
deb http://archive.ubuntu.com/ubuntu bionic-updates main universe
```

```
passwd root
apt update
apt install openssh-server curl python3-pip mc vim resolvconf
systemctl enable sshd
```
* На root по ssh

```
vi /etc/ssh/sshd_config
# Меняем:
PermitRootLogin yes
```
```
# echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
```
```
vi /etc/resolvconf/resolv.conf.d/head
# Дописываем
nameserver 8.8.4.4
nameserver 8.8.8.8
```

```
systemctl enable resolvconf
```

```
exit
```

* Далее по инструкции настраиваем lxc.

```
https://vk.com/@opensuse_os-lxc-i-redmine-dlya-testov
```
```
lxc.rootfs.path = /var/lib/lxc/vixtract/ROOTFS
lxc.include = /usr/share/lxc/config/common.conf
lxc.arch = x86_64
lxc.uts.name  = vixtract
lxc.net.0.type = veth
lxc.net.0.flags = up
lxc.net.0.link = br0
lxc.net.0.ipv4.address = 192.168.0.110/24
# gw это адрес хоста, и он же gw. Я делаю алиас на br0
lxc.net.0.ipv4.gateway = 192.168.0.1
lxc.net.0.name = eth1
lxc.net.0.veth.pair = veth-vixtract
lxc.start.auto = 1

```


* Далее nat на сеть контейнера. У меня контейнер в другой сети поэтому nat. Так то можно обойтись gw в настройках контейнера есть!
* Я сделал так у меня вообще firewalld отключен, хватает их в сети...
```
iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A POSTROUTING -t nat -j MASQUERADE
iptables -A FORWARD -i br0 -j ACCEPT
```
```
echo 1 > /proc/sys/net/ipv4/ip_forward
```
>>> Проект вообще может быть не связон с интернетом. Поэтому после установки systemctl restart firewalld 
>>> А доступ к интерфейсу через проброс портов. Я использую rinetd


* Создаём ещё более безопасного пользователя! Заходим под ним по ssh
* И производим установку по инструкции с самого начала. 
