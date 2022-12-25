# Intent

## Ссылка

Наиболее понятно и подробно описано по ссылке
https://developer.alexanderklimov.ru/android/theory/intent.php

Также официальная документация https://developer.android.com/reference/android/content/Intent

Намерения (intent) могут быть явные или неявные

## Явные намерения

```kotlin
val intent = Intent(context, MainActivity::class.java)
intent.putExtra("FILES_LIST", fileList);
startActivity(intent)
```

putExtra - для передачи информации

startActivity - метод контекста

## Неявные намерения

Существует также неявный вызов активности. В этом случае при создании намерения мы используем не имя класса, а указываем параметры action, data, category с определёнными значениями. Комбинация значений в параметрах определяют цель, которую мы хотим достичь. Это может быть открытие ссылки, отправка письма, набор телефонного номера и т.п. К слову сказать, мы тоже можем прописать в своей активности в фильтрах намерений (Intent Filter) набор тех же параметров: action, data, category. И тогда у пользователя может запуститься не только стандартный браузер, но и наша программа. Иными словами, если параметры намерения совпадают с условиями нашего фильтра, то наше приложение (активность) будет вызвано. Система сканирует активности всех установленных приложений, и если находится несколько подходящих активностей, то Android предоставляет пользователю выбор, какой именно программой следует воспользоваться. Если найдётся только одна подходящая активность, то, естественно, никакого диалога для выбора не будет, и активность запустится автоматически.