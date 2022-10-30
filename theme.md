# Темы

## Ссылки на темы, стили и отдельные части стиля

Ссылка на свой стиль (в том числе для наследования)

```xml
style="@style/StyleName"
```

Но для своего стиля можно (и это предпочтительный способ) наследоваться просто указав имя родительского стиля через точку

```xml
<style name="MyTextStyle.Red">
    <item name="android:textColor">#FF0000</item>
</style>
```

Ссылка на стиль андроида (в том числе для наследования)

```xml
style="@android:style/TextAppearance"
```

Пример наследования

```xml
<style name="GreenText" parent="@android:style/TextAppearance">
    <item name="android:textColor">#00FF00</item>
</style>
```

Знак ? применяется для поиска значения атрибута стиля в текущей теме, а подстрока ?android означает поиск значения атрибута стиля в системной теме Android.

```xml
<EditText
    id="@+id/edit"
    android:textColor:="?android:textColorSecondary" />
```

```xml
<View …
    android:background="?attr/colorSurface"/>
```


TODO: Как ссылаться в svg

## Дополнительная информация

Хорошая вводная [статья на русском](http://developer.alexanderklimov.ru/android/theme.php)

[Статья на английском](https://proandroiddev.com/theming-basics-in-android-13c57bc20605) более подробная, но также для начинающих

https://developer.android.com/reference/android/R.styleable#Theme

## Изменения цвета векторных ресурсов

TODO: