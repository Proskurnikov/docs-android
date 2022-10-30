# Coroutine

## Основы

### Контекст

#### Обработка ошибок

```kotlin
// readDb это Button
readDb.setOnClickListener {
    // Готовим контекст для корутины - обработчик ошибок
    val handler = CoroutineExceptionHandler { coroutineContext, throwable ->
        // просто выведем информацию в лог
        Log.e(TAG, "readDb Coroutine error", throwable)
    }
    // Корутина в Активити, в обработчике нажатия кнопки. используем lifecycleScope - корутина будет связана с жизненным циклом активити и завершит свою работу при завершении работы активити
    // Что бы сработала ошибка, в контексте не указываем диспетчер - по умолчанию Dispatcher.Main - Запускается в основном потоке. Дополнительное условия смотри ниже - требование к вызываемой функции.
    lifecycleScope.launch(handler) {
        // У нас должна быть предварительно подготовлена Room Dao.
        // Что бы сработала ошибка, необходимо что бы getAll() возвращала обычный List<T> не была помечена suspend. В случае если помечена suspend или возвращает LiveData<List<T>> или Flow<List<T>> то ошибки не будет
        val allActions = actionDao.getAll()
        Log.d(TAG, "size = ${allActions.size}")
    }
}
```

## Особенности использования

### В каком потоке может выполняться на примере Room

Room требует выполнения не в основном потоке. Если выполняется в главном потоке приложение упадет с ошибкой.

Для того, что бы функция работала не в основном потоке, надо сделать что то одно:

- возвращаемый тип должен быть `LiveData` или `Flow` (в нашем случае в Dao)
- пометить функцию `suspend` (в нашем случае в Dao)
- запускать в функцию в контексте `Dispatcher.IO` (для операций ввода-вывода) или `Dispatcher.Default` (для cpu-операций)

### Flow

flow.collect {} следует запускать в отдельной корутине, т.к. она запускается в цикле while(true), что бы собирать вновь поступающие данные, следовательно весь код после flow.collect не запустится. 
Это не так. Производитель данных имеет конечное значение в while, но код после все равно не запускается.

### ViewModel Coroutine

В андроид есть специально подготовленные корутины (точнее scope)

Во ViewModel следует использовать viewModeScope

```kotlin
val db getApplication<LylApplication>(appDb
val headerDao = db.headerDao()
viewModelScope.launch {
    header.value?.let {
        headerDao.insert(Header(name = it))
    }
}
```

## Дополнительные материалы

https://medium.com/swlh/kotlin-coroutines-in-android-basics-9904c98d4714

https://medium.com/corouteam/exploring-kotlin-coroutines-and-lifecycle-architectural-components-integration-on-android-c63bb8a9156f

https://developer.android.com/topic/libraries/architecture/coroutines#lifecyclescope

https://proandroiddev.com/kotlin-coroutines-in-andriod-ff0b3b399fa0