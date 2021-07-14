# Room. Шпаргалка. Часть 1

Минимальный набор для запуска Room.

## Подключение к проекту (добавление зависимостей)

Согласно [официальной документации](https://developer.android.com/jetpack/androidx/releases/room#groovy) файл `build.gradle` модуля `app` добавляем

```groovy
plugins {
    //это не указно в официальной документации, но чтобы заработал kapt, надо добавить этот плагин
    // To make enable kapt()
    id "kotlin-kapt"
}
dependencies {
    def room_version = "2.3.0"
    implementation "androidx.room:room-runtime:$room_version"
    annotationProcessor "androidx.room:room-compiler:$room_version"
    // To use Kotlin annotation processing tool (kapt)
    // В официальной документации не указано, но в плагины надо добавить "kotlin-kapt" (см. выше)
    kapt "androidx.room:room-compiler:$room_version"
    // optional - Kotlin Extensions and Coroutines support for Room
    implementation("androidx.room:room-ktx:$room_version")
}
```

Примечание: В официальной документации указано подключение ksp, которое выдает ошибку. Способа избавиться от ошибки не нашел, но если не включать ksp, все и так работает.

```groovy
dependencies {
    def room_version = "2.3.0"
    // To use Kotlin Symbolic Processing (KSP)
    ksp("androidx.room:room-compiler:$room_version")
}
```

## Создание компонентов Room

Создаем Entity, Dao, Database и конвертер.

### Конвертер

Конвертер предназначен для сохранения данных (их называют complex), которые не могут быть сохранены в SQLite ()

В самом простом наборе для запуска конвертер можно не использовать. Но конвертер даты скорее всего понадобится, поэтому сделаем его.

```kotlin
import androidx.room.TypeConverter
import java.sql.Date

class DateConverter {
    @TypeConverter
    fun fromLong(value: Long?) : Date? = value?.let { Date(it) }
    
    @TypeConverter
    fun toLong(date: Date?) : Long? = date?.time
}
```

Методы конвертирования должны быть помечены аннотацией `@TypeConverter`.
Конвертер будет использован далее.

Подробнее можно посмотреть в [документации](https://developer.android.com/training/data-storage/room/referencing-data)

### Entity (модель)

Основная модель данных. 
Создаем data класс. Помечаем аннотацией `@Entity`
id генерируем автоматически аннотацией `@PrimaryKey(autoGenerate=true)`.
Простые типы сохраняются автоматически, для сложных используем ранее подготовленный конвертер.

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey
import androidx.room.TypeConverters
import java.sql.Date

@Entity
data class Action (
    @PrimaryKey(autoGenerate = true)
    var id: Long = 0,
    val name: String,
    @field:TypeConverters(DateConverter::class)
    val date: Date
)
```

Во всех инструкциях указано, что поле необходимо пометить аннотацией `@TypeConverters()`. Но для Kotlin это не работает, так как поле скрыто от нас. Свойство класса следует пометить аннотацией `@field:TypeConverters()`. Аннотацией `@TypeConverters()` можно пометить класс Entity, Dao, Database - все поля, где это необходимо будут правильно конвертированы.

При добавлении вновь созданного объекта в базу данных id не самого объекта не обновляется, а может возвращаться из метода insert() dao-объекта, если если этот метод возвращает Long.

Что еще можно сделать с Entity можно посмотреть на сайте [startandroid.ru](https://startandroid.ru/ru/courses/architecture-components/27-course/architecture-components/530-urok-6-room-entity.html) и в [документации](https://developer.android.com/reference/androidx/room/Entity)

### Dao (Объект доступа к данным)

В Data Access Object описываются методы для работы с базой данных.
Создаем интерфейс с аннотацией `@Dao`.

```kotlin
import androidx.lifecycle.LiveData
import androidx.room.Dao
import androidx.room.Insert
import androidx.room.Query

@Dao
interface ActionDao {
    @Query("select * from `Action`")
    fun getAll(): LiveData<List<Action>>
    
    @Insert
    suspend fun insert(action: Action): Long
}
```

В самом простом примере будем использовать только метод `insert`. Т.к. Room по умолчанию настроен так, чтобы работать не в основном потоке, то методы, не возвращающие LiveData можно отметить ключевым словом `suspend` - так мы не сможем использовать из в основном потоке. У методов, возвращающих LiveData и так асинхронная природа, помечать ключевым словом `suspend` не надо. Insert, если не указан тип возвращаемого значения, ничего не возвращает ), или, если указан тип Long, возращает сгенерированный id.

### Database (основной класс по работе с базой данных)

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase

@Database(entities = [Action::class], version = 1)
abstract class ActionDb : RoomDatabase() {
    abstract fun actionDao(): ActionDao
}
```

Аннотацией Database помечаем основной класс по работе с базой данных. Этот класс должен быть абстрактным и наследовать RoomDatabase.

В параметрах аннотации Database указываем, какие Entity будут использоваться, и версию базы. Для каждого Entity класса из списка entities будет создана таблица.

В Database классе необходимо описать абстрактные методы для получения Dao объектов, которые вам понадобятся.

Создание массива с помощью квадратных скобок возможно только в аннотациях.

## Запуск (первая проверка)

Room по умолчанию требует взаимодействие с базой данных в асинхронном режиме. Будем использовать корутины.

Подключаем LivecycleScope (любая корутина будет отменена когда компонент будет уничтожен - Any coroutine launched in this scope is canceled when the Lifecycle is destroyed).

В зависимости добавляем (только для проверки). В реальном проекте лучше использовать ViewModel и ViewModelScope.

```groovy
dependencies {
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.3.1")
}
```

В активити должно быть.

```kotlin
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import androidx.lifecycle.lifecycleScope
import androidx.room.Room
import kotlinx.coroutines.launch
import java.sql.Date
import java.util.*

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        val button = findViewById<Button>(R.id.createAction)
        //TODO("Change it. Initialize database in application")
        // Создаем базу данных. В реальном проекте создание лучше вынести в Application и сделать lazy (при этом в некоторых источниках не рекомендуется в целом для андроида делать синглтон)
        val db = Room
            .databaseBuilder(this, ActionDb::class.java, "actionsDb")
            .build()
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

Предполагается, что у нас есть layout activity_main на котором размещена кнопка с id = createAction

Все готово, запускаем.
После запуска в DatabaseInspector наблюдаем за изменениями. После нажатия кнопки появляется база данных actionsDb с таблицами Action (наша) и room_master_table.

## Что дальше

### Можно почитать

<https://medium.com/mindorks/room-kotlin-android-architecture-components-71cad5a1bb35>

<https://startandroid.ru/ru/courses/architecture-components/27-course/architecture-components/529-urok-5-room-osnovy.html>

Использование SQLite <https://www.quackit.com/sqlite/tutorial/about_sqlite.cfm>

### Смежные темы

Корутины

### Следующий шаг

Выносим ленивое создание класса базы данных в Application
