# Tesseract

# Быстрый запуск

Воспользуемся самым простым способом на 2024 год.
Мейн документация инструмента: https://github.com/tesseract-ocr/tesstrain

## Требования

1) Tesseract: https://github.com/UB-Mannheim/tesseract/wiki
    - Устанавливаем (и желательно сюда `C:\Program Files (x86)\Tesseract-OCR`)
    - Добавляем в переменные среды в Path
2) Git: Качаем по любой из ссылок
    - https://digi.bib.uni-mannheim.de/tesseract/
    - https://gitforwindows.org/
3) winget: Устанавливаем тоже 
    - https://learn.microsoft.com/en-us/windows/package-manager/winget/
    - Если обычный способ не сработает, то ставим через `choco install wget`
4) Tesstrain: https://github.com/tesseract-ocr/tesstrain
    - Делаем `git clone`
    - Выполняем `pip install -r requirements.txt`

p.s. Чтобы вы не ставили - избегайте кирилицы в путях.

## Подготовка данных 
0) За примером данных далеко ходить не надо. В п.4 мы склонировали репозиторий `tesstrain`. Заглянем в него и найдем `tesstrain/ocrd-testset.zip`. Кто еще не вкурсе нам нужны файлы вида: `image.tif` + `image.gt.txt`
    - `image.tif` - это просто картинка в формате схожим с .png
    - `image.gt.txt` - это разметка картинки (буквами пишем чо на картинке)

1) В дирректори `tesstrain\data` создаем новую папку ОБЯЗАТЕЛЬНО СОВПАДАЮЩУЮ С ШАБЛОНОМ `tesstrain/data/{model_name}-ground-truth`
    - пример: `jopa-ground-truth`

2) На train и target скрипт разделит данные сам


## Обрезание (Cut off the top layer)
- не приходилось еще

## Файн-тюним модель (Fine tune)

На одном уровне с `tesstrain` я создал папку `tessdata` и положил туда модельку `eng.traindata`, которую буду дообучать.

Запускаю дообучение:
```m
$ cd ./tesstrain
$ make training MODEL_NAME=jopa START_MODEL=eng TESSDATA=C:/tessdata MAX_ITERATIONS=10000 LEARNING_RATE=0.001
```

## Обучение с нуля (Retrain from scratch)
- тоже самое, только вместо START_MODEL укажите NET_SPEC

## Как использовать после обучения
Берем полученный `model_name.traindata` файл и кидаем в `C:\Program Files (x86)\Tesseract-OCR\tessdata`

После этого ее можно будет использовать в python:
```python
import pytesseract
text = pytesseract.image_to_string(thresh, config='--psm 6 -c tessedit_char_whitelist=0123456789.:', lang="jopa")
```

# Аналитика

## Графики
Как и сказано в документации, мы может отрисовать графики

```m
$ cd ./tesstrain
$ make training MODEL_NAME=jopa START_MODEL=eng TESSDATA=C:/tessdata MAX_ITERATIONS=10000 LEARNING_RATE=0.001 & make plot MODEL_NAME=jopa
```

## Как улучшить выхлоп:

Работа с данными:
- Аугментация
- Кроп
- Ресайз
- Предобработка

Работа с моделью:
- Выбор архитектуры (RNN, CNN, ...)
- Выбор оптимизатора
- Выбор функции потери (Cross-entropy, CTC)
- Подбор гиперпараметров
- Использование предварительно обученной модели

