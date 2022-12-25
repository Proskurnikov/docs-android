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
    // В этом случае, если каждая Composable функция использует ViewModel, то это будет не одна ViewModel, а независимые, при этом, также при каждом новом вызове Dectination, также будет создаваться новая ViewModel, т.к. у каждого маршрута свой скоуп (временная область, временный специальный контекст)
    composable("profile") { Profile(/*...*/) }
    composable("friendslist") { FriendsList(/*...*/) }
    /*...*/
}
```

Далее обрабатываем нажатия кнопок

```kotlin
navController.navigate("friendslist")
```

Также, при необходимости при обработке нажатия кнопок следует очищать backstack

```kotlin
// Pop everything up to the "home" destination off the back stack before
// navigating to the "friendslist" destination
navController.navigate("friendslist") {
    popUpTo("home")
}

// Pop everything up to and including the "home" destination off
// the back stack before navigating to the "friendslist" destination
navController.navigate("friendslist") {
    popUpTo("home") { inclusive = true }
}

// Navigate to the "search” destination only if we’re not already on
// the "search" destination, avoiding multiple copies on the top of the
// back stack
navController.navigate("search") {
    launchSingleTop = true
}
```

https://developer.android.com/jetpack/compose/navigation#nav-to-composable

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

## Открыть Composable из Notification

Необходимо использовать DeepLink https://proandroiddev.com/open-composables-via-notification-with-jetpack-navigation-b922384f4091

https://developer.android.com/jetpack/compose/navigation#deeplinks

При создании PendingIntent используют TaskStackBuilder 

Здесь https://stackoverflow.com/a/55335775/11596781 объяснение

https://developer.android.com/guide/components/activities/tasks-and-back-stack

Сам класс https://developer.android.com/reference/kotlin/androidx/core/app/TaskStackBuilder

TaskStackBuilder служит, чтобы создать backStack или разместить новую активити на существующий backStack, но если используется Compose с одной активити, то я не вижу смысла использовать TaskStackBuilder, т.к. backStack для навигации Compose все равно сбрасывается.

## Route

Можно создать Route

```kotlin
sealed class Route (val route: String) {
    object Profile: Route("profile")
    object FriendList : Route("friendList") {
        val deepLink = "fn://element"
    }
}
```

И потом использовать

```kotlin
NavHost(navController = navController, startDestination = Route.Profile.route) {
            // В этом случае, если каждая Composable функция использует ViewModel, то это будет не одна ViewModel, а независимые, при этом, также при каждом новом вызове Dectination, также будет создаваться новая ViewModel, т.к. у каждого маршрута свой скоуп (временная область, временный специальный контекст)
            composable(Route.Profile.route) {
                Column {
                    Profile(navController)
                }
            }
            composable(
                route = Route.FriendList.route,
                deepLinks = listOf(
                    navDeepLink { uriPattern = Route.FriendList.deepLink }
                )
            ) {
                Column {
                    FriendsList(navController)
                }
            }
        }
```