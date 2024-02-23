# Доступ к ресурсам

Для доступа к ресурсам следует использовать методы контекста

```kotlin
val name = getString(R.string.channel_name)
```

В случае вызова в Composable функции

```kotlin
val textTitle = context.getString(R.string.notification_title)
```

https://startandroid.ru/ru/courses/compose/30-course/compose/668-urok-5-background-i-border-izobrazhenija.html

Для изображений у нас есть два элемента: Image и Icon

```kotlin
import androidx.compose.foundation.Image
import androidx.compose.material.Icon
import androidx.compose.ui.res.painterResource
 
@Composable
fun HomeScreen() {
    Box(contentAlignment = Center, modifier = Modifier.fillMaxSize()) {
        Image(
            painter = painterResource(id = R.drawable.sand),
            contentDescription = null
        )
        Icon(
            painter = painterResource(id = R.drawable.ic_launcher_foreground),
            contentDescription = null
        )
    }
}
```

Разница между ними понятна из их названий.

Image - для картинок. У него есть стандартные параметры для настройки положения и отображения картинки: alignment, contentScale, alpha, colorFilter.

Icon - для иконок. У него есть параметр tint, который позволяет задать оттенок иконки.