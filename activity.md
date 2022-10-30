# Активити

## Запуск новой активити и возращение на эту же активити с сохраненным статусом

предлагается несколько вариантов

- перехват нажатия up-button (в комбинации с передачей в интент констант-опций)

- передача в интент констант-опций для настройки(очистки) backstack

- настройка активити - launchMode (подробнее о опциях https://medium.com/android-news/android-activity-launch-mode-e0df1aa72242 - здесь также подробности про флаги-константы-опции) - я использовал этот

Ссылки по теме:

https://www.google.com/search?q=android+up+button+save+state&newwindow=1&client=opera&hs=ylt&sxsrf=AOaemvLk1SSNupi8L8huTV0azU8Ls7bdJg%3A1631078521571&ei=eUg4Yf2XIqv3qwHBv7rgBg&oq=android+up+button+save+&gs_lcp=Cgdnd3Mtd2l6EAMYADIICCEQFhAdEB46BwgAEEcQsANKBAhBGABQlbsMWJrHDGCR3AxoAnACeACAAYIBiAGvA5IBAzQuMZgBAKABAcgBCMABAQ&sclient=gws-wiz

https://stackoverflow.com/questions/16354308/restoring-state-in-android-when-using-the-up-button

https://stackoverflow.com/questions/4038479/android-go-back-to-previous-activity

https://stackoverflow.com/questions/37401287/how-to-add-a-listener-for-the-up-button-in-actionbar-on-android

https://stackoverflow.com/questions/6722109/going-two-activities-back?noredirect=1&lq=1

https://stackoverflow.com/questions/4038479/android-go-back-to-previous-activity?noredirect=1&lq=1

Такой способ не предлагался, это были мои мысли (использовать startActivityForResult). Но это надо использовать для других целей. Также, теперь для получения результата из активити рекомендуется использовать другой способ:

https://developer.android.com/training/basics/intents/result

## Завершение активити

activity.finish()

https://stackoverflow.com/questions/15393899/how-to-close-activity-and-go-back-to-previous-activity-in-android