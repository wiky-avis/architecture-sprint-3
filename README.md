# Задание 1. Анализ и планирование

Проект "Smart Home Monolith" представляет собой монолитное приложение для управления отоплением и мониторинга температуры в умном доме. Пользователи
могут удаленно включать/выключать отопление, устанавливать желаемую температуру и просматривать текущую температуру через веб-интерфейс. 
Всё синхронно. Всё управление идёт от сервера к датчику. Данные о температуре также получаются через запрос от сервера к датчику

## Функциональность
**Управление отоплением**

- Пользователи могут включать/выключать отопление;
- Пользователи могут устанавливать желаемую температуру;
- Система автоматически поддерживает температуру.

**Мониторинг температуры**

- Пользователи могут просматривать текущую температуру через веб-интерфейс;
- Система получает данные о температуре с датчиков, установленных в домах.

## Архитектура
**Язык программирования:** `Java`

Плюсы:
- Безопасность и надежность;
- Объектно-ориентированный;
- Архитектурная независимость;
- Простота.

Минусы:
- Низкое, в сравнении с другими языками, быстродействие;
- Повышенные требования к объему оперативной памяти.

**База данных:** `PostgreSQL`

Плюсы:
- расширяемость и богатый набор типов данных;
- масштабируемость;
- кросс-платформенность; 
- безопасность; 
- возможности NoSQL; 
- свободное распространение и открытый код; 
- живое сообщество и исчерпывающее официальное руководство.

Минусы:
- Сложности при настройке;
- Повышенное потребление ресурсов;
- Отсутствие некоторых функций.

**Архитектура:** `Монолитная`

Плюсы:
- Простота и скорость разработки;
- Экономия средств;
- Удобное тестирование;
- Простая инфраструктура.

Минусы:
- Сложности с масштабированием;
- Сложности с развитием функционала;
- Низкая устойчивость к сбоям;
- Ограниченное технологическое разнообразие.

**Взаимодействие:** `Синхронное`

Плюсы:
- Проще в реализации и отладке;
- Легко отслеживать и управлять последовательностью выполнения операций.

Минусы:
- Зависимость от доступности;
- Узкое место. Если синхронные вызовы выполняются последовательно, это может стать узким местом производительности.

**Масштабируемость:** Ограничена, так как монолит сложно масштабировать по частям.

**Развёртывание:** Требует остановки всего приложения.

## Домены и границы контекстов
- Домен: Управление Устройствами
    - Поддомен: управление отоплением
        - Контекст: включение\выключение устройства
        - Контекст: установка температуры
    - Поддомен: автоматическое поддержание температуры
      - Контекст: регулировка подачи тепла  
- Домен: Мониторинг температуры
    - Поддомен: получение данных с датчиков
        - Контекст: получение данных о температуре
    - Поддомен: отображение данных
        - Контекст: отображение текущей температуры в доме

## Проблемы монолитного решения в контексте бизнес-задач компании
Учитывая планы компании на расширение функциональности и масштабировании, с существующим монолитным решением будет сложно:
- гибко масштабировать;
- добавлять новую функциональность и поддерживать;
- развертывание потребует полной остановки сервиса, что повлечет простои в работе;
- ограниченный технологический стек.

## Визуализация контекста системы (As is)
[Context diagram](docs/diagrams/as_is/context/Context.puml)

# Задание 2. Проектирование микросервисной архитектуры
## Декомпозиция приложения на микросервисы
- Домен: Управление Устройствами
    - Поддомен: управление отоплением
        - Контекст: включение\выключение устройства
        - Контекст: установка температуры
    - Поддомен: автоматическое поддержание температуры
      - Контекст: регулировка подачи тепла
    - Поддомен: управление освещением
      - Контекст: включение\выключение света
    - Поддомен: управление воротами
      - Контекст: отпирать\запирать ворота
    - Поддомен: управление виденаблюдением
      - Контекст: влючение/выключение видеонаблюдения за домом
- Домен: Мониторинг температуры
    - Поддомен: получение данных с датчиков
        - Контекст: получение данных о температуре
    - Поддомен: отображение данных
        - Контекст: отображение текущей температуры в доме
- Домен: Сценарии работы устройств
    - Поддомен: управление сценариями
        - Контекст: создание/редактирование/удаление автоматического сценария работы устройств
        - Контекст: просмотр актуального сценария работы устройства
- Домен: Управление пользователями
    - Поддомен: управление пользововательскими данными
        - Контекст: создание/изменение/удаление пользователя
    - Поддомен: назначение ролей пользователю
        - Контекст: назначение/изменение роли пользователю
    - Поддомен: управление уровнем доступа пользователей 
        - Контекст: назначение/изменение разрешений пользователю
    - Поддомен: просмотр данных о пользоватлеях
        - Контекст: просмотр данных о пользователях, ролях и разрешениях

[Context diagram](docs/diagrams/to_be/context/Context.puml)

Исходя из контекста выше, удалось выделить следующие микросервисы:
- **Telemetry Management Service** - Сервис управления телеметрией. Сбор, анализ и хранение телеметрии с устройств;
- **Device Management Service** - Сервис управления устройствами. Хранит данные об устройствах, отправляет команды на устройства;
- **Users Service** - Сервис управления данными о пользователях. Хранит данные о пользователях, ролях и разрешениях;
- **Scenario Service** - Сервис сценариев работы устройств. Хранит сценарии работы устройств.

## Взаимодействие микросервисов
- **API Gateway** направляет запросы к **User Service** для чтения/записи данных о пользователе, роли и разрешении.
- **User Service** направляет запросы к **User DB** читает/сохраняет данные в базу данных.

- **API Gateway** направляет запросы к **Device Management Service** на просмотр данных об устройствах или просмотр/создание сценария работы.
- **Device Management Service** направляет запросы к **DevicesDatabase** на получение данных об устройстве.
- **API Gateway** направляет запросы к **Telemetry Management Service** на получение телеметрии устроиств.
- **Telemetry Management Service** направляет запросы к **TelemetryDatabase** на получение телеметрии устроиств.
- **API Gateway** направляет запросы к **Scenario Service** на просмотр/создание сценария работы устроиства.
- **Scenario Service** направляет запросы к **ScenarioDatabase** на получение/создание сценария работы устроиства.

- **Telemetry Management Service** подписан на события **Kafka** на получение событий по изменению состояний устроиств.
- **Device Management Service** отправляет события **Kafka** на передачу команды на устройство.


## Визуализация архитектуры
[Container Diagram](docs/diagrams/to_be/container/Container.puml)

[Telemetry Management Service Component Diagram](docs/diagrams/to_be/component/TelemetryManagementService_Component.puml)

[Device Management Service Component Diagram](docs/diagrams/to_be/component/DeviceManagementService_Component.puml)

[Scenario Service Component Diagram](docs/diagrams/to_be/component/ScenarioService_Component.puml)

[Users Service Component Diagram](docs/diagrams/to_be/component/UsersService_Component.puml)

[Save Scenario Sequence Diagram](docs/diagrams/to_be/code/SaveScenario_Code.puml)

[Get Telemetry Sequence Diagram](docs/diagrams/to_be/code/GetTelemetry_Code.puml)

[Collect Telemetry Sequence Diagram](docs/diagrams/to_be/code/CollectTelemetry_Code.puml)


# Задание 3. Разработка ER-диаграммы

[ER Diagram](docs/diagrams/to_be/SmartHome_ER.puml)

# Задание 4. Создание и документирование API
[Users Service API](docs/api/UsersServiceApi.yml)

[Scenario Service API](docs/api/ScenarioServiceApi.yml)

[Devices Service API](docs/api/DevicesServiceApi.yml)

[Telemetry Service API](docs/api/TelemetryServiceApi.yml)

[Devices Management Service API](docs/api/DeviceManagementServiceApi.yml)

[Async API](docs/api/asyncapi.yaml)


# Базовая настройка

## Запуск minikube

[Инструкция по установке](https://minikube.sigs.k8s.io/docs/start/)

```bash
minikube start
```

## Добавление токена авторизации GitHub

[Получение токена](https://github.com/settings/tokens/new)

```bash
kubectl create secret docker-registry ghcr --docker-server=https://ghcr.io --docker-username=<github_username> --docker-password=<github_token> -n default
```

## Установка API GW kusk

[Install Kusk CLI](https://docs.kusk.io/getting-started/install-kusk-cli)

```bash
kusk cluster install
```

## Смена адреса образа в helm chart

После того как вы сделали форк репозитория и у вас в репозитории отработал GitHub Action. Вам нужно получить адрес образа <https://github.com/><github_username>/architecture-sprint-3/pkgs/container/architecture-sprint-3

Он выглядит таким образом
```ghcr.io/<github_username>/architecture-sprint-3:latest```

Замените адрес образа в файле `helm/smart-home-monolith/values.yaml` на полученный файл:

```yaml
image:
  repository: ghcr.io/<github_username>/architecture-sprint-3
  tag: latest
```

## Настройка terraform

[Установите Terraform](https://yandex.cloud/ru/docs/tutorials/infrastructure-management/terraform-quickstart#install-terraform)

Создайте файл ~/.terraformrc

```hcl
provider_installation {
  network_mirror {
    url = "https://terraform-mirror.yandexcloud.net/"
    include = ["registry.terraform.io/*/*"]
  }
  direct {
    exclude = ["registry.terraform.io/*/*"]
  }
}
```

## Применяем terraform конфигурацию

```bash
cd terraform
terraform init
terraform apply
```

## Настройка API GW

```bash
kusk deploy -i api.yaml
```

## Проверяем работоспособность

```bash
kubectl port-forward svc/kusk-gateway-envoy-fleet -n kusk-system 8080:80
curl localhost:8080/hello
```

## Delete minikube

```bash
minikube delete
```
