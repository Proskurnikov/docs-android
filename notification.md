# Notifacation

## Notification channel

Создаем канал уведомлений

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

Важность лучше сраpу установить максимальную (после первоначальной установки важность можно поменть вручную или переустановкой, программно не меняется). Если Важность не максимальная? то не получится выводить уведомления со звуком, вибрацией, и не будут появляться всплывающие уведомления ([heads-up](https://developer.android.com/develop/ui/views/notifications#Heads-up))