[Я не туда нажал и хочу обратно в оглавление](./000%20toc.md)
# Устанавливаем Jenkins
## Скачиваем ключ репозитория Jenkins
```wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -```
## Добавляем адрес репозитория Jenkins в список apt

```sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'```

## Получаем обновления из всех репозиториев apt, в том числе и из репозитория Jenkins

```sudo apt-get update```

## Ставим Jenkins

```sudo apt-get install jenkins```

Это займет некоторое время, т.к. пакет у него довольно большой, а скорость, с которой сервер отдает этот пакет — довольно маленькая.

### Проверяем как он установился

```sudo systemctl status jenkins```
Должно быть что-то вроде этого:

![Jenkins has started](./img/013%20JenkinsStarted.png)

### Забираем начальный пароль администратора
Без этого пароля мы не сможем начать работать с Jenkins

```sudo cat /var/lib/jenkins/secrets/initialAdminPassword```

Пароль этот будет уникальным для каждой установки и нужен, чтобы истинный администратор зашел первым и был там администратором всегда, всегда, всегда...

![Jenkins has started](./img/013%20JenkinsOtp.png)

Копируем эту строку в буфер обмена и открываем браузер с адресом:

```http://192.168.1.xxxx:8080```

Если страница долго не открывается и потом вываливается сообщение, что все недоступно, то вероятнее всего это брандмауэр.

С убунту по умолчанию ставится UFW (**U**ncomplicated **F**ire**W**all), проверим, работает ли он:

```sudo systemctl status ufw```

![UFW is started](./img/013%20JenkinsUFWstatus.png)

Ага.

У нас есть два варианта:
1. Вообще отключить UFW (не стоит делать на системах, которые смотрят в интернет или находятся на VPS)
2. Разрешить приложениям работать по порту Jenkins — **8080**

### 1. Вообще выкл
```sudo ufw disable```

Вернуть обратно в работу:

```sudo ufw enable```

### 2. Разрешить порт 8080

```sudo ufw allow 8080```

Я делаю п.2.

Проверяем в браузере:

```http://192.168.1.xxxx:8080```

![UFW is started](./img/013%20JenkinsUnlock.png)

## Customize Jenkins. Плагины.

Выбирайте **Install suggested plug-ins**. Потом ненужное можно будет удалить, а чего не хватает — установим.

## Пользовватель и пароль. 

Всё теже правила: для тестового стенда можно задавать что угодно, для того, что где-то в интернетах или на VPS — что-то сложное и неочевидное.

## Настройка Jenkins

![Manage Jenkins](./img/013%20JenkinsManageJenkins.png)

### Global tool configuration :: JDK > Add JDK

1. Убираем галку Install Automatically.
2. Name: можно написать что угодно, но лучше правду: OpenJDK 11.
3. JAVA_HOME вставляем ```$JAVA_HOME```.
4. Жмём APPLY внизу страницы.

### Global tool configuration :: Git
1. Гит у нас не установлен, ругается, что не может найти поэтому ставим его.

```
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install git
git version
```
2. Жмём SAVE, затем возвращаемся в **Global Tool Configuration**.
3. Git больше не ругается.

### Global tool configuration :: Gradle
1. Add gradle
2. Убираем галку Install Automatically.
3. Name: Default gradle
4. GRADLE_HOME вставляем ```$GRADLE_HOME```.
5. Жмём APPLY внизу страницы.
6. Jenkins ругнётся, что ```$GRADLE_HOME is not a directory on the Jenkins master (but perhaps it exists on some agents)```
7. Жмём SAVE внизу страницы.
8. В терминале выполняем: ```sudo nano /etc/environment```
9. Создаем еще одну строку под JAVA_HOME
10. ```GRADLE_HOME=/home/user/.sdkman/candidates/gradle/current```
11. **Ctrl+o** Enter
12. **Ctrl+x**
13. ```source /etc/environment```
14. ```echo $GRADLE_HOME```

## Проверяем работу
1. Берем какой-нибудь репозиторий, который не требует браузера (API тесты подойдут).
2. Создаем задачу Jenkins и запускаем.
3. Смотрим.

## Настройки системы для Jenkins, плагины
Я намеренно не буду рассказывать это, т.к. это прекрасная исследовательская задача.

Вам будет нужно найти как это сделать и сделать следующее:
1. Установить Allure плагин
2. Установить PostBuild плагин

По умолчанию ни тот, ни другой не устанавливаются.


Далее Устанавливаем Docker и Selenoid, немного правим конфигурацию Jenkins.

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
- [x] [Установим Jenkins.](013%20InstallJenkins.md) :tophat: Done!
- [ ] [Установим Docker.](014%20DockerSelenoid.md)
- [ ] [Установим Selenoid.](014%20DockerSelenoid.md)

[Оглавление](./000%20toc.md)