# Notification

## Создаем Notification channel

Создаем канал уведомлений https://developer.android.com/develop/ui/views/notifications/build-notification#Priority

```kotlin
    private fun createNotificationChannel() {
        // Create the NotificationChannel, but only on API 26+ because
        // the NotificationChannel class is new and not in the support library
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val name = getString(R.string.channel_name)
            val descriptionText = getString(R.string.channel_description)
            val importance = NotificationManager.IMPORTANCE_HIGH
            val channel = NotificationChannel(PROCESS_OF_TEST_NOTIFICATION_CHANNEL_ID,
                name,
                importance).apply {
                description = descriptionText
            }
            // Register the channel with the system
            val notificationManager: NotificationManager =
                getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
            notificationManager.createNotificationChannel(channel)
        }
    }
```

Создавать его желательно в application. 

Важность лучше сразу установить максимальную (после первоначальной установки важность можно поменть вручную или переустановкой, программно не меняется). Если Важность не максимальная? то не получится выводить уведомления со звуком, вибрацией, и не будут появляться всплывающие уведомления ([heads-up](https://developer.android.com/develop/ui/views/notifications#Heads-up))

При создании канала уведомлений необходимо импортировать

```kotlin
import android.app.NotificationChannel
import android.app.NotificationManager
import android.content.Context
import android.os.Build
```

Также необходимо подготовить:

- имя канала уведомлений (String) Сохранить в ресурсах R.string.channel_name
- описание канала уведомлений (String) Сохранить в ресурсах R.string.channel_description
- id канала уведомлений (String) Можно сохранить как константу на уровне корневого namespace приложения

Id канала уведомления будет далее использоваться при создании уведомлений.

## Создаем уведомление

Создаем самое простое уведомление

```kotlin
            var builder = NotificationCompat.Builder(context, PROCESS_OF_TEST_NOTIFICATION_CHANNEL_ID)
                .setSmallIcon(R.drawable.notification_icon)
                .setContentTitle(notificationTitle)
                .setContentText(notificationContent)
                .setPriority(NotificationCompat.PRIORITY_HIGH)
```

Перед создание уведомления необходимо подготовить:

- заголовок уведомления (String) Сохранить в ресурсах R.string.notification_title
- содержание уведомления (String) Сохранить в ресурсах R.string.notification_content
- иконку Сохранить в ресурсах R.drawable.ic_launcher_foreground

В качестве id (String) канала уведомлений ранее созданный id

## Показываем уведомление

```kotlin
with(NotificationManagerCompat.from(context)) {
    // notificationId is a unique int for each notification that you must define
    notify(notificationId, builder.build())
}
```

Необходимо определить notificationId: Int

## Обработка нажатия на уведомление

Подготавливаем intent и PendingIntent

```kotlin
val intent = Intent(context, MainActivity::class.java)
val pendingIntent = PendingIntent.getActivity(context, 0, intent, 0)
```

В билдер уведомления добавляем

```kotlin
.setContentIntent(pendingIntent)
.setAutoCancel(true)
```

.setAutoCancel(true) убирает уведомление, когда пользователь нажимает на него

Полностью

```kotlin
val notificationTitle = context.getString(R.string.notification_title)
val notificationContent = context.getString(R.string.notification_content)
val notificationId = 1
val intent = Intent(context, MainActivity::class.java)
val pendingIntent = PendingIntent.getActivity(context, 0, intent, 0)
var builder =
    NotificationCompat.Builder(context, PROCESS_OF_TEST_NOTIFICATION_CHANNEL_ID)
        .setSmallIcon(R.drawable.notification_icon)
        .setContentTitle(notificationTitle)
        .setContentText(notificationContent)
        .setPriority(NotificationCompat.PRIORITY_HIGH)
        .setContentIntent(pendingIntent)
        .setAutoCancel(true)
with(NotificationManagerCompat.from(context)) {
    // notificationId is a unique int for each notification that you must define
    notify(notificationId, builder.build())
}
```
