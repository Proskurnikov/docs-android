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

### ViewModel

По [ссылке](https://developer.android.com/jetpack/compose/state#viewmodels-source-of-truth) указано, что экземпляр ViewModel не следует передавать вниз в другие Composable функции.

По [сссылке](https://developer.android.com/jetpack/compose/interop/compose-in-existing-arch) указано, что, в случае использования нескольких Composable функций в одной активити, то они будут использовать одну ViewModel, но если при этом используется Navigation, то если Composable является точкой назначения навигационного графа, то не будет использована одна ViewModel.