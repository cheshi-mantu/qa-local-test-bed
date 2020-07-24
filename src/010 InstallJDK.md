[Я не туда нажал и хочу обратно в оглавление](./000%20toc.md)
# Установка JDK
Это будет быстро.
## Проверяем наличие уже установленной Java 
В терминале (теперь это putty или git bash) проверяем, а нет ли уже установленной Java (нужно войти на удаленную машину, конечно):

```java --version```

Терминал нам ответит что-то похожее на это, если Java не установлена:
```
user@qa-test-bed:~$ java --version
Command 'java' not found, but can be installed with:
sudo apt install default-jre
sudo apt install openjdk-11-jre-headless
sudo apt install openjdk-8-jre-headless
```
## Выполняем установку JDK
### Проверяем обновления

```sudo apt-get update && sudo apt-get upgrade```

### Устанавливаем JDK

```sudo apt-get install default-jdk```

Соглашаемся на то, что установщик предлагает (```Y```)

Когда бурление в терминале закончится, проверяем, что мы сделали: 

```java --version```

Друго дело:

![picture](./img/010%20JDKInstalled.png)

## Прописываем переменную окружения JAVA_HOME

### Куда эта open JDK поставилась?

Пытаемся узнать. Терминал:

``` sudo find / -type d -name "*openjdk*" | grep "openjdk-amd"```

Команда осуществляет поиск папок, у которых в строке есть подстрока **openjdk** и затем grep нам это подсвечивает красным (можно было это и не делать).

![find and grep](./img/010%20JDKfoundDir.png)

Посмотрим, что там есть в этой папке

```ls /usr/lib/jvm/java-11-openjdk-amd64```

```ls``` показывает содержимое папки (то же самое, что например в Windows делает ```dir```)

![picture](./img/010%20JDKFolderContent.png)

Раз там есть ```bin, include, lib```, похоже, что это действительно то место, куда JDK установлено.

### Проверяем, задана ли переменная JAVA_HOME

>Переменные окружения и другие переменные, которые использует линуксовый shell должны использоваться со знаком доллара перед именем переменной, иначе это будет просто строка.

```echo $JAVA_HOME```

Если вы видите пустой экран, то переменная не задана и нам нужно будет ее задать.

### Прописываем переменную JAVA_HOME в /etc/environment

Запускаем текстовый редактор **nano**, который устанавливается вместе с Убунту и открываем в нем файл environment из папки /etc

```sudo nano /etc/environment```

откроется черное страшное окно, где нам нужно будет перейти в конец имеющейся строки, нажать Enter (переходим на новую строку) и добавить без пробелов следующее:
```JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64```

Выглядеть это будет так:

![Adding JAVA_HOME](./img/010%20JdkEnvJavaHomeAdd.png)

Чтобы сохранить изменения нужно нажать ```Ctrl + O``` и потом сразу **Enter**
Чтобы выйти из редактора: ```Ctrl + X```

### Обновляем данные о переменных окружения

```source /etc/environment```

### Проверяем, что переменная JAVA_HOME теперь задана

```echo $JAVA_HOME```

![JAVA_HOME is set now](./img/010%20JdkJavaHomeIsNowSet.png)

Теперь можно устанавливать SDK и затем Gradle

- [x] [Создадим виртуальную машину.](005%20vm%20and%20ubuntu.md)
- [x] [Установим на нее Ubuntu.](005%20vm%20and%20ubuntu.md)
- [x] [Проверим, что все установилось.](006%20checkWeAreOkay.md) 
- [x] [Да! Еще выучим пару команд линуха.](006%20checkWeAreOkay.md)
- [x] [Настроим ssh на локальной Windows машине.](007%20sshLocalWindows.md)
- [x] [Настроим ssh сервер на Ubuntu.](008%20sshOnVm.md)
- [x] [Настроим аутентификацию при помощи публичного ключа на Ubuntu.](009%20ssh-passwordless.md)
- [x] [Начнем работать с Ubuntu только через терминал.](009%20ssh-passwordless.md)
- [x] [Установим JDK.](010%20InstallJDK.md) :wrench:
- [ ] [Установим SDK.](011%20SDK.md)
- [ ] Установим Gradle.
- [ ] Установим Jenkins.
- [ ] Установим Docker.
- [ ] Установим Selenoid.

[Оглавление](./000%20toc.md)