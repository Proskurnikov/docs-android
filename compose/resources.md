# Доступ к ресурсам

Для доступа к ресурсам следует использовать методы контекста

```kotlin
val name = getString(R.string.channel_name)
```

В случае вызова в Composable функции

```kotlin
val textTitle = context.getString(R.string.notification_title)
```