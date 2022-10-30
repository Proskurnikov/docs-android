# RecyclerView

## Минимальный код для запуска RecyclerView

На основе [материалов](http://developer.alexanderklimov.ru/android/views/recyclerview-kot.php)

Создаем layout для одного элемента списка

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    >
    
    <TextView
        android:id="@+id/recyclerItemProperty_tv_propertyName"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="8dp"
        android:layout_marginTop="8dp"
        android:text="TextView"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        />
</androidx.constraintlayout.widget.ConstraintLayout>
```

По умолчанию layout создается на основе ConstraintLayout - меня это устраивает. Главное, что бы `android:layout_height="wrap_content"`, иначе заполнится вся оставшаяся часть экрана, и не будут отображаться все элементы кроме первого. Аналогично, если элементы списка располагаются горизонтально, следует заменить `android:layout_width="wrap_content"`.

Добавляем RecyclerView в активити.

```xml
<androidx.constraintlayout.widget.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            tools:context=".addUnit.AddUnitActivity"
            >

<androidx.recyclerview.widget.RecyclerView
                android:id="@+id/addUnit_recycler_properties"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:layout_marginTop="8dp"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toBottomOf="@+id/addUnit_tv_date"
                />

        </androidx.constraintlayout.widget.ConstraintLayout>
```

Про особенности размещения в NestedScrollView смотри далее.

Создаем адаптер.

Итоговый адаптер:

```kotlin
import a.proskurnikov.logyourlife.R
import a.proskurnikov.logyourlife.model.ExtendedProperty
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.TextView
import androidx.recyclerview.widget.RecyclerView

class PropertyRecyclerViewAdapter(private val properties: List<ExtendedProperty>)
    : RecyclerView.Adapter<PropertyRecyclerViewAdapter.ViewHolder>(){
    class ViewHolder(itemView: View): RecyclerView.ViewHolder(itemView) {
        private var property: ExtendedProperty? = null
        
        private val propertyName = itemView.findViewById<TextView>(R.id.recyclerItemProperty_tv_propertyName)
        
        init {
            itemView.setOnClickListener {
            
            }
        }
    
        fun bind(property: ExtendedProperty) {
            this.property = property
            propertyName.text = currentProperty?.name
        }
    }
    
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val itemView = LayoutInflater.from(parent.context)
            .inflate(R.layout.recycler_item_property_add_unit, parent, false)
        return ViewHolder(itemView)
    }
    
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.bind(properties[position])
    }
    
    override fun getItemCount(): Int = properties.size
}
```

Пошаговое создание адаптера:

В конструктор адаптера передаем список

```kotlin
class PropertyRecyclerViewAdapter(private val properties: List<ExtendedProperty>) {
}
```

Добавляем ViewHolder

```kotlin
class PropertyRecyclerViewAdapter(private val properties: List<ExtendedProperty>) {
    class ViewHolder(view: View): RecyclerView.ViewHolder(view) {
    
    }
}
```

Во вьюхолдере находим все компоненты (все View в layout для одного элемента списка). Также дополнительно (при необходимости) в блоке init устанавливаем обработку событий). Также рекомендуется создать метод bind, передать один элемент списка в ViewHolder и привязку к компонентам проводить в этом методе (в этом случае, все ссылки на компоненты можно сделать приватными). Далее метод bind будет вызван в методе onBindViewHolder адаптера (или проведена привязка компонентов к значениям).

```kotlin
class PropertyRecyclerViewAdapter(private val properties: List<ExtendedProperty>) {
    class ViewHolder(itemView: View): RecyclerView.ViewHolder(itemView) {
        private var property: ExtendedProperty? = null
        
        private val propertyName = itemView.findViewById<TextView>(R.id.recyclerItemProperty_tv_propertyName)
        
        init {
            itemView.setOnClickListener {
            
            }
        }
    
        fun bind(property: ExtendedProperty) {
            this.property = property
            propertyName.text = currentProperty?.name
        }
    }
}
```

Наследуем адаптер от RecyclerView.Adapter<PropertyRecyclerViewAdapter.ViewHolder> и реализуем три метода

```kotlin
class PropertyRecyclerViewAdapter(private val properties: List<ExtendedProperty>)
    : RecyclerView.Adapter<PropertyRecyclerViewAdapter.ViewHolder>(){
    class ViewHolder(itemView: View): RecyclerView.ViewHolder(itemView) {
        private var property: ExtendedProperty? = null
        
        private val propertyName = itemView.findViewById<TextView>(R.id.recyclerItemProperty_tv_propertyName)
        
        init {
            itemView.setOnClickListener {
            
            }
        }
    
        fun bind(property: ExtendedProperty) {
            this.property = property
            propertyName.text = currentProperty?.name
        }
    }
    
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        TODO("Not yet implemented")
    }
    
    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        TODO("Not yet implemented")
    }
    
    override fun getItemCount(): Int {
        TODO("Not yet implemented")
    }
}
```

Количество элементов

```kotlin
override fun getItemCount(): Int = properties.size
```

Создание ViewHolder

```kotlin
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int)ViewHolder {
    val itemView = LayoutInflater.from(parent.context)
        .inflate(R.layout.recycler_item_property_add_unit, parent, false)
    return ViewHolder(itemView)
}
```

Привязываем данные

```kotlin
override fun onBindViewHolder(holder: ViewHolder, position: Int) {
    holder.bind(properties[position])
}
```

подключаем к активити

```kotlin
binding.addUnitRecyclerProperties.layoutManager LinearLayoutManager(this)
binding.addUnitRecyclerProperties.adapter PropertyRecyclerViewAdapter(listOf(
    ExtendedProperty(1,"test", "test",1L, null),
    ExtendedProperty(1,"test", "test",1L, null),
))
```

Проверяем.

Далее:

- получаем реальные данные;
- привязываем оставшиеся данные.

Если реальные данные получаем в LiveData, создание и привязку адаптера следует разместить в наблюдателе за этими данными

```kotlin
model.schema.observe(this) {
    ...
    binding.addUnitRecyclerProperties.adapter PropertyRecyclerViewAdapter(it.properties)
}
```

### Особенности размещения в NestedScrollView

При размещении RecyclerView в NestedScrollView все работает как ожидается - прокрутка идет у NestedScrollView. У RecyclerView можно отключить 

```xml
android:nestedScrollingEnabled="false"
```

или кодом

```kotlin
binding.addUnitRecyclerProperties.isNestedScrollingEnabled = false
```

тогда поведение (отображение) немного изменится. Например, исчезнут лишние элементы у RecyclerView

![RecyclerViewNestedScrollViewIsFalse](recyclerview\RecyclerViewNestedScrollViewIsFalse.png) ![RecyclerViewNestedScrollViewIsTrue](recyclerview\RecyclerViewNestedScrollViewIsTrue.png)
*Обратите внимание на отсутствие анимации у RecyclerView на верхнем рисунке*

## Обновление списка

### Методами

adapter.notifyDataSetChanged()

### DiffUtil

## Paginating

https://ziginsider.github.io/large-database-queries-on-android/
https://medium.com/androiddevelopers/large-database-queries-on-android-cb043ae626e8

https://github.com/android/architecture-components-samples/tree/master/PagingSample

## Декоратор

## Жесты
