# Column

## Выравнивание содержимого

```kotlin
Column(
        modifier = Modifier.fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
) {
        Text(
                text = "First item",
                modifier = Modifier.padding(16.dp)
        )
        Text(
                text = "Second item",
                modifier = Modifier.padding(16.dp)
        )
        Text(
                text = "Third item",
                modifier = Modifier.padding(16.dp)
        )
}
```

https://stackoverflow.com/a/62152703/11596781

## Добавление прокрутки

```kotlin
    val scrollState = rememberScrollState() 

    Column(
        modifier = Modifier
            .fillMaxSize()
            // Это для прокрутки
            .verticalScroll(scrollState),
        horizontalAlignment = Alignment.CenterHorizontally,
    )
    {
        ...
    }
```