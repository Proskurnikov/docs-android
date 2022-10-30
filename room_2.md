# Room. Шпаргалка. Часть 2

//TODO: Рекомендуемый способ создания объекта базы данных с использованием внедрения зависимостей (так жу создается синглтон, только с использованием специальных DI библиотек (Hilt Dagger)). Ну и получается, все это надо переделать на использование Hilt.

Выносим создание класса базы данных в Application. Используем синглтон, как указано в официальном [примере](https://developer.android.com/codelabs/android-room-with-a-view-kotlin#0), хотя в сети указывают на множество [недостатков](https://programmerr47.medium.com/singletons-in-android-63ddf972a7e7) синглтона самого по себе и для [андроида](https://stackoverflow.com/questions/16517702/singleton-in-android) в частности, все равно создание базы данных выносится в Application, где по сути получаем тот же синглтон.

## Создаем синглтон для базы данных

Обычно нужен только один экземпляр базы данных Room для всего приложения

В класс базы данных

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase

@Database(
    entities = [Action::class],
    version = 1
)
abstract class ActionDb : RoomDatabase() {
    abstract fun actionDao(): ActionDao
}
```

добавляем синглтон

```kotlin
import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase

@Database(
    entities = [Action::class],
    version = 1
)
abstract class ActionDb : RoomDatabase() {
    abstract fun actionDao(): ActionDao
    
    companion object {
        private var INSTANCE: ActionDb? = null
        
        fun getDatabase(context: Context) : ActionDb =
            INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context,
                    ActionDb::class.java,
                    "actionsDb",
                ).build()
                INSTANCE = instance
                instance
        }
    }
}
```

## Создаем Application в котором получаем базу данных

Т.к. объект базы данных тяжеловесный, то следует использовать один экземпляр для всего приложения, используя контекст приложения

Создаем класс приложения

```kotlin
import android.app.Application

class RoomApplication : Application() {
    val actionDb by lazy {
        ActionDb.getDatabase(this)
    }
}
```

Не забываем добавить созданный класс приложения в манифест

```xml
<application
    android:name=".RoomApplication"
    ...
    >
    ...
</application>
```

## Получаем объект базы данных

В нужном месте получаем объект базы данных

```kotlin
val db = (application as RoomApplication).actionDb
```

В нашем случае меняем код активити

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    
        val button = findViewById<Button>(R.id.createAction)
       
        //Новое
        val db = (application as RoomTest3Application).actionDb

        val actionDao = db.actionDao()
        button.setOnClickListener {
            lifecycleScope.launch {
                val date = Calendar.getInstance()
                actionDao.insert(
                    Action(name = "test", date = Date(Calendar.getInstance().time.time))
                )
            }
        }
    }
}
```

Запускаем.

## Pagination

https://medium.com/swlh/paging3-recyclerview-pagination-made-easy-333c7dfa8797