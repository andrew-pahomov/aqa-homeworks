# Домашнее задание к лекции «Состояние, зависимости, CI»

## Задача №1 - Настройка CI

Вы уже научились в прошлом ДЗ писать авто-тесты. Самое время выстроить полностью автоматическую систему прогона этих тестов.

Что надо сделать:

1. Берёте проект с предыдущей лекции с сервисом, настраиваете для него CI (см. инструкцию ниже). Удостоверяетесь, что CI показывает, что сборка падает (в процессе сборки будут автоматически прогоняться все авто-тесты)
2. Далее берёте вторую версию сервиса, заменяете предыдущую, удостоверяетсь, что ваши тесты проходят локально, и пушите всё в GitHub. После чего удостоверяетесь, что в CI тесты тоже проходят.

Вторая версия сервиса:

```java
package ru.netology.service;

public class CashbackHackService {
    private final int boundary = 1000;

    public int remain(int amount) {
        boolean isNeedMore = amount % bonusBound != 0;
        if (!isNeedMore) {
            return 0;
        }

        int remain = bonusBound - amount % bonusBound;
        return remain;
    }
}
```

### AppVeyor

[AppVeyor](https://www.appveyor.com) - одна из платформ, предоставляющих функциональность Continuous Integration. В базовом варианте - бесплатна.

#### Шаг 0. Конфигурация как код

Поскольку вручную настраивать каждый проект в системе Continuos Deployment - лишняя трата времени, мы будем хранить всю конфигурацию для AppVeyor в специальном файле с названием `.appveyor.yml`

Файл этот должен храниться в самом репозитории на GitHub, тогда AppVeyor будет автоматически подхватывать настройки из него:

![](https://i.imgur.com/Gg7B961.png)

Yaml - формат сериализации данных, используемый многими системами для хранения конфигурации.

Ссылки:
* [Wikipedia](https://en.wikipedia.org/wiki/YAML)
* [Спецификация](https://yaml.org/spec/1.2/spec.html)

Странички на Wikipedia достаточно для понимания базовых конструкций языка.

AppVeyor позволяет использовать все три оболочки (CMD, PS, Bash), но мы для простоты на Linux будем использовать Bash, на Windows - CMD.

##### Linux Config

```yaml
image: Ubuntu1804  # образ для сборки

branches:
  only:
    - master  # ветка git

build: off  # будем использовать свой скрипт сборки

build_script:
  - ./gradlew build
```

##### Windows Config

```yaml
image: Visual Studio 2015  # образ для сборки

branches:
  only:
    - master # ветка git

build: off  # будем использовать свой скрипт сборки

build_script:
  - gradlew build
```

Задача `build` запускает не только компиляцию приложения, но и прогон авто-тестов.

#### Шаг 1. Регистрация

![](https://i.imgur.com/Rugmz7D.png)


#### Шаг 2. Регистрация через GitHub

Appveyor предоставляет бесплатный тарифный план для публичных репозиториев GitHub (авторизация - также через GitHub):

![](https://i.imgur.com/jXvftMb.png)

#### Шаг 3. Разрешение доступа

При подключении необходимо разрешить Appveyor получать уведомления:

![](https://i.imgur.com/2Fvcj96.png)

#### Шаг 4. Создание проекта

После авторизации станет доступной панель управления, где можно создать новый проект:

![](https://i.imgur.com/wUBKbYY.png)


Авторизуйте AppVeyor в качестве OAuth App:

![](https://i.imgur.com/oQadLLj.png)

Это даст возможность приложению получать уведомления о ваших `push` в репозиторий, модификации и т.д.

![](https://i.imgur.com/2jwH6Sa.png)

Детальнее об OAuth вы можете прочитать на:
* https://oauth.net/2/
* https://auth0.com/docs/protocols/oauth2


#### Шаг 5. Выбор репозитория

После авторизации достаточно будет нажать кнопку `ADD` напротив необходимого репозитория:

![](https://i.imgur.com/4VQME6j.png)


После настройки всего процесса каждый `push` в ветку `master` GitHub-репозитория будет приводить к запуску сборки на AppVeyor.

#### Шаг 6. Status Badge

На странице `Settings` - `Badges` Appveyor предлагает код для "бейджика" статуса вашего проекта:

![](https://i.imgur.com/DECtZjg.png)


Этот badge необходимо разместить в файле `README.md` для отображения текущего статуса вашего проекта:

![](https://i.imgur.com/V9cOeJO.png)


## Задача №2 - Parametrized Test (необязательно)

Обновите предыдущий проект, заменив обычные тесты параметризоваными (аннотация `@ParametrizedTest`).

Тестовые данные должны храниться в CSV-файле.

Полученное решение также залейте на Github (делайте всё в рамках одного проекта) и удостоверьтесь, что сборка по-прежнему работает.