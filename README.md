## Запуск

Чтобы запустить проект, выполните команду 
```bash
make deploy
```
## Задача
Необходимо реализовать сервис для расчета проектной 
аналитики на основе данных из различных популярных сервисов, таких 
как Notion, Trello, YouGile и т.д.

1. Каждый сервис имеет свое апи и свой набор сущности в этих пределах
2. С какой бы частотой мы не скачивали данные, они всегда недостаточно актуальны

## Идея
Вместо разработки архитектуры базы данных 
для хранения результатов, приходящих со 
стороны API, необходимо создать кэш-хранилище

Так мы сможем регулировать нагрузку на сеть, 
доступ к данным, тем не менее, также будет быстр

Не выделяя общей схемы мы сможем полагаться 
на уникальную логику каждого API, не теряя 
возможностей для расчета аналитических данных. 
При этом мы также можем выделять общие схемы 
между различными API, устраняя повторение кода

## Реализация
Можно выделить основные кодовые блоки:

### 1. Клиентный
Место унифицированного доступа к API и точка кэширования данных

Реализовано в виде блока классов, наследованных от общего BaseClient, 
каждый из которых описывает правила взаимодействия с соответствующим API

### 2. Аналитический
Место реализации функций аналитических метрик

Реализовано в виде классовых функций, внутри каждой есть 
метод для расчета значений метрики при определенном наборе 
аргументов и фабричный метод, который, в зависимости от выбранного
клиента формирует входные аргументы на основе кэшированных данных

### 3. API
API для пользователей, регистрирующихся на нашем сервисе

Реализовано стандартными инструментами Django

## Усложнение кэша
На данном этапе используется redis, однако, 
в случае большого объема данных можно использовать 
2 подхода для повышения качества решения

1. Чтобы снять нагрузку с сети, сделать кэш 
многоуровневым, чтобы не забывать 
часто используемые данные, работать оно должно 
наподобие того, как кэш устроен в операционной системе

2. Чтобы снять нагрузку с оперативной памяти,
в качестве промежуточного хранилища добавить 
noSQL базу данных, например mongoDB для сохранения 
части данных прямо на диск
