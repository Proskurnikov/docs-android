# Compose

## Сохранение состояния

### rememberSavable

Чтобы сохранить состояние при смене конфигурации (например, при повороте экрана) следует использовать такую конструкцию

```kotlin
var startPressure by rememberSaveable { mutableStateOf("") }
```

Чтобы все нормально работало, не забываем импортирвать, иначе предыдущая конструкция выдаст ошибку

```kotlin
import androidx.compose.runtime.setValue
import androidx.compose.runtime.getValue
```

`remember` не сохраняет состояние при смене конфигурации, только при рекомпозиции.

О других способах можно посмотреть в [справке](https://developer.android.com/jetpack/compose/state) и [ответе](https://stackoverflow.com/a/66178263/11596781) и [метанит](https://metanit.com/kotlin/jetpack/5.2.php)

### ModelView

