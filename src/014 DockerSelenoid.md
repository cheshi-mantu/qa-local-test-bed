[Я не туда нажал и хочу обратно в оглавление](./000%20toc.md)
# Устанавливаем Selenoid
Эта штука нужна нам для того, чтобы можно было запускать UI тесты с браузерами.

## Предварительные шаги
Менее болезненно всегда использовать опыт чужих ошибок, а не совершать свои. Поэтому я сразу говорю, что Selenoid по умолчанию работает на том же порту (8080), что и Jenkins. Таким образом я вернул вам некоторое количество времени, которое вы могли потратить на совершенно прекрасную ругань внутри головы и поиск места, где же вы ошиблись и проч.  

### Jenkins переезжает на порт 8888
>Я предлагаю Jenkins перекинуть на другой порт, например, на 8888.

Конфиг, где указан порт, который будет слушать Jenkins лежит вот тут: ```/etc/default/jenkins```

Правим этот файл:
```
sudo nano /etc/default/jenkins
```
Ищем вот эту строку и меняем порт с **8080** на **8888**:

![Jenkins port to 8888](./img/014%20SelenoidJenkinsOtherPort.png)

```Ctrl+o > Enter > Ctrl + x```

Разрешаем работать на новом порту (терминал):
```
sudo ufw allow 8888
```

Делаем для Jenkins restart:

```
sudo service jenkins restart
```


Теперь нужно зайти в конфигурацию Jenkins: ```Manage Jenkins > Configure System > Jenkins location:```

![Jenkins settings port to 8888](./img/014%20SelenoidJenkinsSettingJenkinsLocation.png)

Это нужно, чтобы Jenkins "сам мог себя найти" (такое описание у этой настройки, я ничего не придумал).

Теперь мы готовы устанавливать Docker и Selenoid

## Устанавливаем Docker

Можно прочитать на [оф. сайте, эти команды взяты оттуда](https://docs.docker.com/engine/install/debian/)

Можно прочитать копипасту:

1. Проверяем/удаляем старые версии Docker
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
Обновляемся 
```
sudo apt-get update
```
и ставим нужные для установки докера библиотеки:
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

Добавляем официальный GPG ключ Docker:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Проверяем, что у нас ключ с верным отпечатком ```9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88```
```
sudo apt-key fingerprint 0EBFCD88
```
Добавляем стабильный репозиторий:
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
Ставим Docker после обновления репозиториев:

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
Проверяем, что Docker корректно установлен:
```
sudo docker run hello-world
```
### Можно сделать так, чтобы для запуска Docker не требовалось выполнять sudo
Следующие три команды добавят текущего пользователя в группу docker (она вероятнее всего уже была создана при установке самого докера):
```
sudo groupadd docker
sudo usermod -aG docker ${USER}
newgrp docker
```
Всё, дальше можно не использовать sudo.


## Устанавливаем менеджер конфигураций для Selenoid

Установка описана на [странице Aerokube](https://aerokube.com/cm/latest/), тут копипаста. 

### Установка CM (Configuration Manager - менеджер конфигураций)
CM управляет конфигами Selenoid и делает за вас всю работу, если вам не нужно что-то нестандартное.

В терминале вероятнее всего мы находимся в папке /home/user но на всякий случай перейдем в нее и потом установим:
```
cd ~
curl -s https://aerokube.com/cm/bash | bash
```
CM скачается и расскажет, что можно с его помощью делать:
![picture](./img/014%20SelenoidCMDownloaded.png)

### Запуск Selenoid
```
sudo ./cm selenoid start --vnc
```
CM начнет скачивать всё, что ему нужно для работы (Pulling image, Fetching image и проч.) и затем выдаст сообщение, о том, что Selenoid запущен.

### Проверка, работает ли Selenoid

Открываем браузер, набираем:

```http://192.168.1.78:4444/status```

Мы должны в окне браузера получить такой ответ:
```{"total":5,"used":0,"queued":0,"pending":0,"browsers":{"chrome":{"83.0":{},"84.0":{}},"firefox":{"77.0":{},"78.0":{}},"opera":{"68.0":{},"69.0":{}}}}```

Это означает, что Selenoid действительно работает.

### Заходим в Selenoid UI
Стартуем через CM:

```sudo ./cm selenoid-ui start```

CM скачивает и запускает UI, пользовательский интерфейс будет доступен по адресу:
``` 
http://192.168.1.78:8080/
```

Мы готовы тестировать UI на виртуальной машине.

Так как и Jenkins и Selenoid установлены на одной и той же машине, то обращаться к удаленному web-драйверу можно и, наверное, даже нужно по адресу ```http://127.0.0.0.1```,
т.е. настройки для Gradle будут выглядеть следующим образом:

```
-Dremote_driver_url='http://127.0.0.1:4444/wd/hub'
-Dvideo_storage_url='http://127.0.0.1:8080/video'
```
Далее добавляем плагины в Jenkins и все, что работало на тестовом стенде школы, будет работать и у вас на виртуальной машине.
- [x] [Создадим виртуальную машину.](005%20vm%20and%20ubuntu.md)
- [x] [Установим на нее Ubuntu.](005%20vm%20and%20ubuntu.md)
- [x] [Проверим, что все установилось.](006%20checkWeAreOkay.md) 
- [x] [Да! Еще выучим пару команд линуха.](006%20checkWeAreOkay.md)
- [x] [Настроим ssh на локальной Windows машине.](007%20sshLocalWindows.md)
- [x] [Настроим ssh сервер на Ubuntu.](008%20sshOnVm.md)
- [x] [Настроим аутентификацию при помощи публичного ключа на Ubuntu.](009%20ssh-passwordless.md)
- [x] [Начнем работать с Ubuntu только через терминал.](009%20ssh-passwordless.md)
- [x] [Установим JDK.](010%20InstallJDK.md) 
- [x] [Установим SDK.](011%20SDK.md) 
- [x] [Установим Gradle.](012%20GradleInstall.md)
- [x] [Установим Jenkins.](013%20InstallJenkins.md)
- [x] [Установим Docker.](014%20DockerSelenoid.md) :whale2:
- [x] [Установим Selenoid.](014%20DockerSelenoid.md) :octocat: :thumbsup:
[Оглавление](./000%20toc.md)