# Практика 2 - Основы разработки API

# Темы: Node.js, NestJS

### Основное задание

#### Установка node.js

Для работы с Node.js необходимо установить платформу Node.js на компьютер (Подойдет любая ОС: Windows, Linux, Mac OS).

Для начала проверьте, не установлен ли Node.js

```
node -v
```

Если Node.js ранее был установлен, то команда выведет версию ноды. Иначе будет ошибка

Для установки воспользуйтесь инструкцией на официальном сайте https://nodejs.org

#### Установка NestJS

Для удобной разработки бэкенда на ноде используется фреймворк NestJS. Его задача - упростить взаимодействие с нодой и операционной системой для более простой разработки API.

Для начала также проверим, не установлен ли NestJS с помощью команды

```
nest -v
```

Если NestJS ранее был установлен, то команда выведет версию ноды. Иначе будет ошибка

Если не установлен, то установите с помощью команды

```
npm i -g @nestjs/cli
```

npm - менеджер пакетов, встроенный в Node.js. С помощью него устанавливаются все необходимые зависимости (библиотеки, модули...). В данном случае `i` - сокращение от install (установить), а `-g` означает глобальную установку модуля (то есть модуль доступен всему компьютеру, а не только в одном конкретном проекте).
Ознакомиться с списком всех команд можно через команду

```
npm --help
```

Аналогично можно ознакомиться с командами nest или node

```
node --help
nest --help
```

#### Создание проекта на NestJS

Для создания проекта воспользуемся командой

```
nest new <название проекта>
```

Название проекта можно выбрать любое. Пример:

```
nest new practice-api
```

В процессе создания nest предложит выбрать менеджер пакетов. Если не знаете чем они между собой отличаются и не знаете какой выбрать, выбирайте `npm`.

Далее NestJS создаст папку с проектом. Ознакомьтесь с созданными папками и файлами (структурой проекта). Основные папки и файлы:

- **`src/`** — основная папка с исходным кодом приложения. Здесь пишется логика работы сервера.
- **`src/app.module.ts`** — главный модуль приложения, который связывает все остальные модули, контроллеры и сервисы.
- **`src/app.controller.ts`** — контроллер по умолчанию. Принимает входящие запросы и отправляет ответы.
- **`src/app.service.ts`** — сервис по умолчанию. Хранит бизнес-логику приложения, которую вызывают контроллеры.
- **`src/main.ts`** — точка входа в приложение. Именно здесь запускается сервер NestJS.
- **`package.json`** — список зависимостей проекта и полезные команды (например, `npm run start` для запуска сервера).
- **`package-lock.json`** — автоматически создаваемый файл, фиксирует точные версии установленных зависимостей. Благодаря этому проект можно точно восстановить на другом компьютере.
- **`tsconfig.json`** — настройки TypeScript (что компилировать, как проверять ошибки и т.д.).
- **`nest-cli.json`** — настройки Nest CLI (например, какая папка считается исходной).
- **`.eslintrc.js` / `.prettierrc`** — правила проверки и автоматического форматирования кода, чтобы проект выглядел аккуратно и одинаково у всех.
- **`node_modules/`** — папка, в которой хранятся все внешние библиотеки, установленные через `npm`. Обычно её не редактируют вручную и не загружают в репозиторий.

#### Добавляем эндпоинты

Продолжим создание процесса регистрации и входа. На прошлом занятии мы сделали форму регистрации и входа. В этом сделаем API для него. API для этого процесса будет создержать два запроса (эндпоинта)

```
POST /auth/login
Body: {
    login: string;
    password: string;
}
```

```
POST /auth/register
Body: {
    name: string;
    surname: string;
    email: string;
    password: string;
    gender: string;
    age: number;
}
```

Для начала создадим модуль для логики, связанной с авторизацией и аутентификацией. Назовём его auth. Создать можно самостоятельно, а можно через командой. Воспользуемся командой

```
nest g mo auth
```

В данном случае `g` означает `generate`, `mo` - `module`, `auth` - название модуля

В консоле будет подобный вывод:

```
CREATE src/auth/auth.module.ts (81 bytes)
UPDATE src/app.module.ts (308 bytes)
```

Он говорит о том, какие файлы были созданы или обновлены

Также сгенерируем в модуле сервис и контроллер. Для этого выполним еще две команды

```
nest g co auth
nest g s auth
```

В код добавятся сервис и контроллер и файлы тестов для них (файлы тестов содежат в названии слово `.spec`. Например `auth.service.spec.ts`). На данный момент на файлы тестов можно не обращать внимания (даже если в них есть ошибки. На работу приложения файлы тестов не влияют). Также при генерации контроллер и сервис были добавлены в модуль

Файл src/auth/auth.module.ts

```ts
import { Module } from "@nestjs/common";
import { AuthService } from "./auth.service";
import { AuthController } from "./auth.controller";

@Module({
  providers: [AuthService],
  controllers: [AuthController],
})
export class AuthModule {}
```

Откроем контроллер `src/auth/auth.contoller.ts` и добавим туда два эндпоинта. Файл контроллера - это точка входа для запроса. В них не долго содержаться бизнес-логики. Контроллеры существуют чтобы принимать запросы и возвращать ответ. Для бизнес-логики существуют сервисы.

```ts
import { Body, Controller, Post } from "@nestjs/common";

@Controller("auth")
export class AuthController {
  @Post("login")
  public login(@Body() dto) {
    console.log("Call login endpoint", dto);
    return { message: "ok" };
  }

  @Post("register")
  public register(@Body() dto) {
    console.log("Call login endpoint", dto);
    return { message: "ok" };
  }
}
```

В коде выше мы добавили два эндпоинта работающих по методу HTTP POST. Создали мы его через декоратор `@Post()`. Декоратор это специальная функция, позволяющая добавить специальную логику. Декораторы начинаются с символа `@`. `Controller`, `Post`, `Get`, `Put`, `Body` - это всё декораторы.

Декторатор `@Controller` делает наш класс контроллером и добавляет префикс в URL `auth` (либо иной, который мы указали в скобках). Это означает, что контроллер будет обрабатывать запросы к URL `auth/*`. Далее мы добавили эндпоинты login и register. А зачит их URL будет `/auth/login` и `/auth/register` соответсвенно.

С помощью декоратора `@Body` мы можем получить данные, которые передаются в body.

Данный код сейчас не выполняет никакой логики, а только выводит в консоль информацию о вызванном эндпоинте.

Попробуем запустить код через команду

```
npm run start:dev
```

Если всё хорошо, то в косоль выведется несколько строк, среди которых должна быть строка, означающаяя успешный запуск кода

```
[Nest] 21630  - 10/03/2025, 3:56:04 PM     LOG [NestApplication] Nest application successfully started +1ms
```

Теперь наш API доступен по адресу `http://localhost:3000` (порт 3000 стоит по умолчанию).

Протестировать можно через Postman или через браузер.

Через Postman протестировать можно как на скриншоте

![Скриншот 1](/lesson_3/images/screen_1.png "Тестирование через Postman")

Либо через браузер через простую форму (пример: файл login.html). Если работать с чистым HTML, то для того, чтобы форма работала, нужно:

- Форма была в теге `<form>`
- У тега `<form>` должны быть описаны `action` (url эндпоинта) и `methon` (HTTP-метод)
- У полей стоит атрибут `name`. Значение атрибута должно совпадать с полями, которые мы ожидаем в API

При нажатии на кнопку "Войти" произойдет редирект на страницу с сообщением `{"message":"ok"}`. Это нормально. Эта форма только для тестирования. Красивый фронтенд мы сделаем в следующих практиках. Также можно отследить что отправлятся через инструменты разработчика в браузере во вкладке Network

![Скриншот 2](/lesson_3/images/screen_2.png "Тестирование через браузер")

В консоли соответсвенно будет выведено сообщение с параметрами, которые мы передали в Body

![Скриншот 3](/lesson_3/images/screen_3.png "Сообщение в консоли")

#### Делаем валидацию

Запомним важное правило

> Никогда нельзя доверять данным, передаваемым с фронтенда. Всё нужно перепроверять

Поэтому входные данные нужно проверять на корректность. Этот процесс называется валидацией. В NestJS для таких целей используется библиотека class-validator.

Библиотека в GitHub: https://github.com/typestack/class-validator
Документация по использованию библиотеки в NestJS: https://docs.nestjs.com/techniques/validation

Установим библиотеку с помощью команды

```
npm i --save class-validator class-transformer
```

Включим валидацию. Для этого в файле src/main.ts добавим строку `app.useGlobalPipes(new ValidationPipe())`. В итоге получим такой код

```
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe());
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

_P.S. Не забываем импортировать `ValidationPipe`_

Теперь нам нужно описать _DTO (Data Transfer Object - объект передачи данных_) для работы. В папке `auth` создадим еще папку `dto`. В ней мы создадим два файла:

- login.dto.ts
- register.dto.ts

Опишем DTO для login.dto.ts

```ts
import { IsEmail, IsNotEmpty, IsString, MinLength } from "class-validator";

export class LoginDto {
  @IsNotEmpty()
  @IsEmail()
  login: string;

  @IsString()
  @IsNotEmpty()
  @MinLength(8)
  password: string;
}
```

DTO для валидации представляет из себя класс, с полями. Для каждого поля валидация добавляется через декораторы. В данном случае декораторы означают следующее:

- IsNotEmpty - Поле должно быть передано и не должно быть пустым
- IsEmail - Поле должно быть корректным E-mail адресом
- IsString - Поле должно быть строкой
- MinLength - Длина переданной строки должно иметь минимальную длину... (в нашем случае минимум 8 символов)
  Больше декораторов можно узнать в документации class-transformer

Для использования DTO ее необходимо указать в качестве типа данных возле тега `@Body`. Пример:

```ts
import { Body, Controller, Post } from "@nestjs/common";
import { LoginDto } from "./dto/login.dto";

@Controller("auth")
export class AuthController {
  @Post("login")
  public login(@Body() dto: LoginDto) {
    console.log("Call login endpoint", dto);
    return { message: "ok" };
  }

  @Post("register")
  public register(@Body() dto) {
    console.log("Call login endpoint", dto);
    return { message: "ok" };
  }
}
```

_P.S. Не забываем импортировать LoginDto_

Теперь если мы попробуем передать некорректные данные в запросе, то у нас будет соотвествующая ошибка.

Если передадим вместо строки число в поле `password`
![Скриншот 4](/lesson_3/images/screen_4.png "Валидация скриншот 1")

Если передадим невалидный E-mail в поле `login`
![Скриншот 5](/lesson_3/images/screen_5.png "Валидация скриншот 2")

#### Самостоятельное задание 1

Самостоятельно создайте и подключите DTO для валидации данных для регистрации. Набор полей для регистрации должен соответсвовать полям из предыдущей практики.

#### Подключение Swagger

Swagger - автоматически генерируемая документация API. Генерируется она на основе декораторов. Для начала нужно подключить к проекту swagger. Устанавливается он через команду

```
npm install --save @nestjs/swagger
```

Далее необходимо подключить конфигурацию в src/main.ts файле. Для этого нужно добавить следующий код в функции `bootstrap`

```ts
const config = new DocumentBuilder()
  .setTitle("API для лабораторных")
  .setDescription(
    "Описание API для лабораторных работ по предмету 'Введение в профессию'"
  )
  .setVersion("1.0")
  .addTag("auth")
  .build();
const documentFactory = () => SwaggerModule.createDocument(app, config);
SwaggerModule.setup("api", app, documentFactory);
```

Теперь откроем `auth.controller.ts` и добавим классу декоратор `@ApiTags('auth')`

```ts
import { Body, Controller, Post } from "@nestjs/common";
import { LoginDto } from "./dto/login.dto";
import { ApiTags } from "@nestjs/swagger";

@Controller("auth")
@ApiTags("auth")
export class AuthController {
  @Post("login")
  public login(@Body() dto: LoginDto) {
    console.log("Call login endpoint", dto);
    return { message: "ok" };
  }

  @Post("register")
  public register(@Body() dto) {
    console.log("Call login endpoint", dto);
    return { message: "ok" };
  }
}
```

Теперь откроем сваггер в браузере по адресу `http://localhost:3000/api`

![Скриншот 6](/lesson_3/images/screen_6.png "Swagger скриншот 1")

Однако мы в этой документации не можем увидеть тело запроса и поля, которые необходимо передавать в эндпоинтах. Для этого нужно модифицировать DTO. Для каждого поля добавить декоратор `@ApiProperty`

```ts
import { ApiProperty } from "@nestjs/swagger";
import { IsEmail, IsNotEmpty, IsString, MinLength } from "class-validator";

export class LoginDto {
  @ApiProperty()
  @IsNotEmpty()
  @IsEmail()
  login: string;

  @ApiProperty()
  @IsString()
  @IsNotEmpty()
  @MinLength(8)
  password: string;
}
```

С этим правками сваггер будет выглядеть следующим образом

![Скриншот 7](/lesson_3/images/screen_7.png "Swagger скриншот 2")

#### Самостоятельное задание 2

Самостоятельно добавьте описание эндпоинта регистрации в Swagger

#### Добавление простой логики

В этом задании не будем подключать базу данных, а попробуем простую логику. Базы данных задействуем в слудующих занятиях. Откоем файл auth.service.ts и добавим следующий код

```ts
import { Injectable, UnauthorizedException } from "@nestjs/common";

@Injectable()
export class AuthService {
  private users: { login: string; password: string }[] = [
    {
      login: "test@test.local",
      password: "testPwd!23",
    },
    {
      login: "ivan@test.local",
      password: "qwqrty1234",
    },
  ];

  public login(login: string, password: string) {
    const user = this.users.find((user) => user.login === login);

    if (!user) {
      throw new UnauthorizedException("Неверный логин или пароль");
    }

    if (user.password === password) {
      return user;
    } else {
      throw new UnauthorizedException("Неверный логин или пароль");
    }
  }
}
```

Конструкция `throw new UnauthorizedException("Неверный логин или пароль");` в коде называется исключением (_Exception_). Используется она для обозначения ошибок. При вызове исключения выполнение кода прерывается и пользователю возвращается ошибка.

И теперь подключим сервис в контроллере. Для этого нужно в классе `AuthController` добавить контроллер и внутри контроллера прописать зависимость.

```ts
constructor(private authService: AuthService) {}
```

Далее модифицируем метод `login` чтобы проверять логин и пароль пользователя

```ts
  @Post('login')
  public login(@Body() dto: LoginDto) {
    this.authService.login(dto.login, dto.password);
    return { message: 'ok' };
  }

```

Теперь можно проверить через Postman или через форму `login.html` вход в приложение. Если мы будем передавтаь верные данные для входа, то получим ответ `{"message": "ok"}`. Иначе будет ошибка

#### Самостоятельное задание 3

Напишите логику для регистрации. Для этого нужно добавить в сервисе метод register. Данные пользователя при регистрации нужно добавить в массив users.

_Подсказка: у массива есть метод .push()_

_P.S. Так как мы не подключали базу данных, то данные нового пользователя будут храниться только пока запущено приложение. Если мы перезапустим приложение, то данные удалятся. Пока что это нормальное поведение. Сохранение пользователя навсегда добавим в следующих лабораторных работах._

### Дополнительные материалы

1. [Статья] Как лучше называть эндпоинты https://tproger.ru/translations/luchshie-praktiki-razrabotki-rest-api-20-sovetov
2. [Статья] Что такое CRUD https://ru.hexlet.io/courses/http-api/lessons/crud/theory_unit
3. [Видео] Что такое CRUD за 6 минут https://youtu.be/vD0X5Zm9Gjo?si=Bb1M9JQaKPhweSLx
4. [Видео] ВСЕ МЕТОДЫ HTTP за 14 минут https://youtu.be/_fEzV5IFtjE?si=I2sHTemGAAI-NX9R
5. [Видео] Введение в REST API за 7 минут https://youtu.be/cDdSzwU2Bic?si=fvrgahSqWZ9Dn0SM
6. [Видео] Что такое TypeScript за 9 минут https://youtu.be/w3bOHpu0z9o?si=2nwjbbbjnw5vXMGK
