# Compose Navigation

## Минимальные шаги, что бы все заработало

Хорошо подходит для использования со Scaffold. Простое использование достаточно простое.

Добавляем зависимость в app.gradle

```groovy
implementation "androidx.navigation:navigation-compose:2.5.3"
```

Добавляем сохраняющий состояние контроллер. Добавить его надо в одном блоке перед Scaffold (в котором его будем использовать, ну или перед тем местом где будем использовать)

```kotlin
val navController = rememberNavController()
```

Создаем NavHost туда, где должна использоваться навигация (например в Scaffold(content = {NavHost ...}))

```kotlin
NavHost(navController = navController, startDestination = "profile") {
    composable("profile") { Profile(/*...*/) }
    composable("friendslist") { FriendsList(/*...*/) }
    /*...*/
}
```


Далее обрабатываем нажатия кнопок

```kotlin
navController.navigate("friendslist")
```

## Простой пример использования co Scaffold

```kotlin
@OptIn(ExperimentalMaterialApi::class)
@Composable
fun BasicScaffold() {
    val navController = rememberNavController()
    val scaffoldState = rememberScaffoldState()
    val coroutineScope = rememberCoroutineScope()

    Scaffold(
        scaffoldState = scaffoldState,
        topBar = {
            TopAppBar {
                IconButton(onClick = {

                    coroutineScope.launch {
                        scaffoldState.drawerState.open()
                    }
                }) {
                    Icon(Icons.Filled.Menu, contentDescription = "Menu")
                }
                Text(
                    text = stringResource(id = R.string.app_name)
                )
            }
        },
        drawerContent = {
            ListItem(
                text = { Text("Главная") },
                icon = { Icon(Icons.Filled.AccountBox, "") },
                modifier = Modifier.clickable {
                    navController.navigate("profile")
                    coroutineScope.launch { scaffoldState.drawerState.close() }
                }
            )
            Divider()
            ListItem(
                text = { Text("friendslist") },
                icon = { Icon(Icons.Filled.AccountBox, "") },
                modifier = Modifier.clickable {
                    navController.navigate("friendslist")
                    coroutineScope.launch { scaffoldState.drawerState.close() }
                }
            )
        }
    ) {
        NavHost(navController = navController, startDestination = "profile") {
            composable("profile") { Text(text = "profile") }
            composable("friendslist") {  Text(text = "friendslist") }
        }
    }
}
```

## Подробности, возможности, примеры

Более подробно в [справке](https://developer.android.com/jetpack/compose/navigation)

[Пример Scaffold с Navigation](https://stackoverflow.com/a/73295465/11596781)

## Выбранное значение маршрута

Получить выбранное значение маршрута

```kotlin
val currentRoute = navController.currentBackStackEntry?.destination?.route
```

Если необходимо хранить как состояние

```kotlin
val navBackStackEntry by navController.currentBackStackEntryAsState()
val currentRoute = navBackStackEntry?.destination?.route
```

которое можно использовать для выделения пункта, например, в Drawer для Scaffold

```kotlin
                            Row(
                                modifier = Modifier
                                    .fillMaxWidth()
                                    .height(56.dp)
// Пример использования:
// Меняем фон, когда выбран этот путь
.background(if ("openAir" == currentRoute) MaterialTheme.colors.secondary else colorResource(id = android.R.color.transparent))
                                    .clickable {
                                        navController.navigate("openAir")
                                        coroutineScope.launch { scaffoldState.drawerState.close() }
                                    },
                                verticalAlignment = Alignment.CenterVertically,
                            ) {
                                Text("На открытом воздухе",
                                    modifier = Modifier.padding(8.dp))
                            }
```

[Подробнее](https://stackoverflow.com/questions/66493551/jetpack-compose-navigation-get-route-of-current-destination-as-string)
