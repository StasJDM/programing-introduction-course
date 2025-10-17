# Практика 3 - Изучаем модули NestJS

# Темы: Node.js, NestJS, cron, InfoSec

### Основное задание

За основу этого задание берется кодавая база из прошлого занятия.

#### CRON

Cron используется для планирования задач. Подробней о cron можно почитать в интернете (в конце файла есть ссылки на видосы и статьи). Для использования cron установим библиотеку в наш проект `(P.S. Нужно находиться в папке проекта, выполяя эту команду)`.

```
npm install --save @nestjs/schedule
```

Затем нужно в app.module.ts добавить в список импортов `ScheduleModule`. В результате код будет выглядить следующим образом

```ts
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";
import { AuthModule } from "./auth/auth.module";
import { ScheduleModule } from "@nestjs/schedule";

@Module({
  imports: [AuthModule, ScheduleModule.forRoot()],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

Добавим новый сервис. Например, в модуле `auth`. Для этого в папке `auth` создадим файл `cron.service.ts`. Код пустого сервиса будет выглядеть так:

```ts
import { Injectable } from "@nestjs/common";

@Injectable()
export class CronService {}
```

Не забываем добавлять к классу декоратор `@Injectable()`. Он нужен для работы механизма DI (Dependency Injection, подробнее можно почитать в интернете).

Теперь добавим метод и добавим для него декоратор @Cron(), внутри которого передадим паттерн Cron.

```ts
import { Injectable } from "@nestjs/common";
import { Cron } from "@nestjs/schedule";

@Injectable()
export class CronService {
  @Cron("30 * * * * *")
  every30Seconds() {
    console.log("Этот лог будет появляться каждые 30 секунд");
  }
}
```

Также в этой библиотеке есть набор готовых популярных Cron-паттернов, которые содержатся в перечислении `CronExpression` (в коде ниже такой декоратор добавлен к методу every30SecondsV2).
Также можно добавлять периодические задачи через декоратор `@Interval()`. Для этого декоратору необходимо передать интервал в милисекундах (в коде ниже такой декоратор добавлен к методу every30SecondsV3)

```
import { Injectable } from '@nestjs/common';
import { Cron, CronExpression, Interval } from '@nestjs/schedule';

@Injectable()
export class CronService {
  @Cron('30 * * * * *')
  every30Seconds() {
    console.log('Этот лог будет появляться каждые 30 секунд');
  }

  @Cron(CronExpression.EVERY_30_SECONDS)
  @Cron('30 * * * * *')
  every30SecondsV2() {
    console.log('Этот лог тоже будет появляться каждые 30 секунд');
  }

  @Cron(CronExpression.EVERY_30_SECONDS)
  @Interval(30000)
  every30SecondsV3() {
    console.log('И этот лог будет появляться каждые 30 секунд');
  }
}

```

`P.S. Не забываем добавлять новый сервис в список провайдеров модуля`

Подробнее о cron в NestJS можно почитать в документации: https://docs.nestjs.com/techniques/task-scheduling

#### Самостоятельное задание по cron

Создать методы, которые выполняются:

1. Каждые 3 минуты
2. Каждые 4 минуты 28 секунд
3. Каждые 10 минут

#### Самостоятельное задание - Rate limiting

В приложениях обычно для защиты от DDos-атак используют ограничения на количество запросов. В NestJS для решения этой задачи есть встроенная библиотека.
Для того, чтобы библиотека понимала, когда и для кого нужно ограничить запросы, она хранит в памяти данные о том, сколько запросов каждый пользователь отпраляет. Хранить данные можно в памяти приложения, а можно в Redis. В нашем случае реализуем вариант по-умолчанию (в памяти приложения).

Ссылка на документацию: https://docs.nestjs.com/security/rate-limiting

#### Самостоятельное задание - Helmet

Для защиты от некоторых вид хакерских атак есть библиотека Helmet. Самостоятельное задание заключается в том, чтобы узнать от каких атак защищает Helmet и подключить Hemlmet к приложению.

Ссылка на документацию: https://docs.nestjs.com/security/helmet

### Дополнительные материалы

1. [Видео] Запуск задач по расписанию linux. Cron и его маленький секрет https://youtu.be/ynK_9Q7tkVk?si=uPecpzASDRfQGq5p
2. [Статья] Cron в Linux: история, использование и устройство https://habr.com/ru/companies/badoo/articles/468061/
3. [Статья] Основы внедрения зависимостей https://habr.com/ru/articles/434380/
4. [Статья] Загадка от Жака Фреско: как построить свой Rate Limiter и не утонуть в море компромиссов https://habr.com/ru/companies/ddosguard/articles/908662/
5. [Статья] Сравнение алгоритмов ограничения частоты запросов https://habr.com/ru/companies/ruvds/articles/816243/
