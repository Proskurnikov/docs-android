# ViewModel

Подключаем

```gradle
implementation "androidx.lifecycle:lifecycle-viewmodel-compose:2.5.1"
```

Создаем uiState

```kotlin
data class ProfileUiState(val name: String, val countClick: Int = 0)
```

Создаем viewModel

```kotlin
class ProfileViewModel : ViewModel() {
    var profileUiState by mutableStateOf(ProfileUiState(name = "Android"))
        private set

    init {
        viewModelScope.launch {
            delay(1000)
            profileUiState= profileUiState.copy(countClick = profileUiState.countClick + 1)
        }
    }

    fun update() {
        profileUiState = profileUiState.copy(countClick = profileUiState.countClick + 1)
    }
}
```

Во viewModel для compose можно сразу использовать State. Также можно использовать StateFlow или LiveData, а в @Composable преобразовывать в State:

- LiveData.observeAsState() included in the androidx.compose.runtime:runtime-livedata:$composeVersion artifact.
- Flow.collectAsState() doesn't require extra dependencies.

При этом Flow во viewModel лучше преобразовать в StateFlow со следующими настройками

```kotlin
    val numberFlow = 
    // flow для примера, обычно он должен быть получен, например, из репозитория
    flow {
        (1..5).forEach{
            delay(1000)
            emit(it)
        }
    }.stateIn(
        initialValue = "Initial Value",
        scope = viewModelScope,
        started = WhileSubscribed(5000)
    )
```

В @Composable создаем viewModel и используем его. В дочерние Composable передавать не рекомендуется, надо передавать только данные.

```kotlin
fun Profile(
    navController: NavHostController,
    viewModel: ProfileViewModel = androidx.lifecycle.viewmodel.compose.viewModel(),
) {
    val number = viewModel.numberFlow.collectAsState(initial = 0)

    AppBar(navController = navController)
    Text(text = viewModel.profileUiState.name)
    Button(onClick = {
        viewModel.update()
    }) {
        Text(text = viewModel.profileUiState.countClick.toString())
    }
    Text(text = number.value.toString())

    BackStackInfo(backQueue = navController.backQueue)
    NotificationButton()
}
```







```kotlin

```