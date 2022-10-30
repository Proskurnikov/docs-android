# EditText

## Получение объекта EditText из TextInputLayout

Объект EditText можно получить как обычно, назначив ему id.

Если у нас есть объект TextInputLayout с назначенным id, он обычно должен содержать в себе один объект EditText (в нашем случае подкласс из материальных компонентов TextInputEditText). В этом случае EditText можно получить

```kotlin
val textInputLayout = findViewById<TextInputLayout>(R.id.textInputLayout)
val editText = textInputLayout.editText
```

## Отслеживание изменений

Отслеживать изменения в текстовом поле (набор текста) надо с помощью объекта [TextWatcher](https://developer.android.com/reference/android/text/TextWatcher.html) в котором определены три метода:

- afterTextChanged,
- beforeTextChanged,
- onTextChanged.

Для каждого из трех методов определен метод расширения (имя метода = do + имя метода TextWather) для TextView в ktx ([исходный код](https://android.googlesource.com/platform/frameworks/support/+/android-room-release/core/ktx/src/main/java/androidx/core/widget/TextView.kt)). [Документация по методам расширения](https://developer.android.com/reference/kotlin/androidx/core/widget/package-summary)

Подключаем ktx к проекту

```gradle
implementation 'androidx.core:core-ktx:1.0.0'
```

[Документация по core-ktx](https://developer.android.com/jetpack/androidx/releases/core)

Пример использования метода расширения

```kotlin
// binding.header - получаем доступ к объекту EditText с использованием ViewBinding
binding.header.doOnTextChanged { text, start, before, count ->
    Log.d(TAG, "header.doOnTextChanged text = $text, start = $start, " +
            "before = $before, count = $count")
    // передаем значение во ViewModel
    model.header.value = text.toString()
}
```

Или метод для использования объекта TextWatcher

```kotlin
binding.header.addTextChangedListener(// здесь передаем TextWatcher)
```

## Дополнительные материалы

<https://material.io/components/text-fields>

<https://stackoverflow.com/questions/40569436/kotlin-addtextchangelistener-lambda>

<https://developer.android.com/reference/kotlin/androidx/core/widget/package-summary>