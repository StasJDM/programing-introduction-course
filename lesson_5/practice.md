# Практика 4 - Генерация excel в Node.js

## Темы: Node.js, Excel, axios

### Основное задание

Создадим папку для нового проекта. В папке создадим файл index.js. Теперь в папке с этим файлом добавим npm:

```
npm init -y
```

Теперь у нас есть файл package.json. Теперь добавим два модуля:

- axios - для вызова внешнего API
- exceljs - для работы с Excel

```
npm install axios exceljs
```

Для примера возьмем API для прогноза погоды.

Open-Meteo предоставляет бесплатный доступ к прогнозу погоды без API-ключа.

Пример запроса для получения ежедневного прогноза на 7 дней для Москвы:

```
GET https://api.open-meteo.com/v1/forecast?latitude=55.75&longitude=37.62&daily=temperature_2m_max,temperature_2m_min,windspeed_10m_max&forecast_days=7
```

Параметры:

latitude, longitude — координаты города.
daily=... — перечисление нужных ежедневных показателей.
forecast_days=7 — количество дней прогноза (максимум 7 в бесплатной версии).
Координаты популярных городов:

Москва: 55.75, 37.62
Санкт-Петербург: 59.94, 30.31
Новосибирск: 55.01, 82.93

В Postman запрос выглядит так:

![Скриншот 1](/lesson_5/images/screen_1.png "Тестирование API через Postman")

Теперь откроем файл index.js и вставим напишем следующий код:

```js
const axios = require("axios");

async function fetchWeatherForecast() {
  const city = "Москва";
  const lat = 55.75;
  const lon = 37.62;

  try {
    const response = await axios.get("https://api.open-meteo.com/v1/forecast", {
      params: {
        latitude: lat,
        longitude: lon,
        daily: "temperature_2m_max,temperature_2m_min,windspeed_10m_max",
        forecast_days: 7,
      },
    });

    console.log("Ответ API: ", response.data);
  } catch (e) {
    console.log("Произошла ошибка: ", e);
  }
}

fetchWeatherForecast();
```

И теперь запустим написанную программу:

```
node index.js
```

По итогу мы должны увидеть в консоли такой результат, который мы получили из API

![Скриншот 2](/lesson_5/images/screen_2.png "Результат выполнения скрипта")

Теперь вставим полученные данные в Excel таблицу. Для этого добавим в начале файла импорт библиотеки ExcelJS

```js
const ExcelJS = require("exceljs");
```

А ниже, после получения ответа от API добавим следующий код:

```js
const workbook = new ExcelJS.Workbook();
const worksheet = workbook.addWorksheet("Прогноз на неделю");

worksheet.columns = [
  { header: "Дата", key: "date", width: 12 },
  { header: "Город", key: "city", width: 15 },
  { header: "Макс. темп. (°C)", key: "maxTemp", width: 18 },
  { header: "Мин. темп. (°C)", key: "minTemp", width: 18 },
  { header: "Макс. ветер (км/ч)", key: "wind", width: 20 },
];

for (let i = 0; i < daily.time.length; i++) {
  worksheet.addRow({
    date: daily.time[i],
    city: city,
    maxTemp: daily.temperature_2m_max[i],
    minTemp: daily.temperature_2m_min[i],
    wind: daily.windspeed_10m_max[i],
  });
}

const filename = `прогноз_погоды_${Date.now()}.xlsx`;
await workbook.xlsx.writeFile(filename);
console.log(
  `✅ Прогноз успешно сохранён в ${`прогноз_погоды_${Date.now()}.xlsx`}`
);
```

В итоге после запуска программы в папке рядом с пограммой появится excel-файл подобного вида:
![Скриншот 3](/lesson_5/images/screen_3.png "Полученный Excel-файл")


### Индивидуальное задание

Выбрать любую открытыю API и написать программу, которая получит из API данные и запишет их в excel-файл. 