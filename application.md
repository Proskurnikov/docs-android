# Application

## Когда нужен

Создание своего объекта приложения может понадобиться в случаях:

- создание синглтона для Room (т.к. объект Room тяжеловесный и лучше его иметь один для контекста приложения)
- регистрация Broadcast receiver для контекста приложения
- создания каналов для уведомлений (channel_id for notification)

## Как сделать

Создаем класс приложения

```kotlin
import android.app.Application

class MyApplication : Application() {

}
```

Не забываем добавить созданный класс приложения в манифест

```xml
<application
    android:name=".MyApplication"
    ...
    >
    ...
</application>
```