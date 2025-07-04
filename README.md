# VK - Тестовое задание

## Data

Внимательно просмотрев разметку видео, я обнаружил неточности и внёс следующие корректировки в интервалы заставок:

---

### 1. Train data

| Видео                                                                                                 | Было             | Стало            |   
|-------------------------------------------------------------------------------------------------------|------------------|------------------|
| [220020068_456255414.mp4](https://vkvideo.ru/video-220020068_456255414)                               | 0:01:42–0:01:47  | 0:00:42–0:00:47  |
| [220020068_456241671.mp4](https://vkvideo.ru/video-220020068_456241671)                               | 0:03:47–0:03:10  | 0:02:47–0:03:10  |
| [220020068_456256016.mp4](https://vkvideo.ru/video-220020068_456256016)                               | 0:04:32–0:04:38  | 0:03:32–0:03:38  |
| [220020068_456255332.mp4](https://vkvideo.ru/video-220020068_456255332)                               | 0:02:57–0:02:25  | 0:01:57–0:02:57  |

---

### 2. Test data

| Видео                                                                                               | Было             | Стало            |
|-----------------------------------------------------------------------------------------------------|------------------|------------------|
| [220020068_456241671.mp4](https://vkvideo.ru/video-220020068_456241671)                             | 0:03:47–0:03:10  | 0:02:47–0:03:10  |
| [220020068_456256475.mp4](https://vkvideo.ru/video-220020068_456256475)                             | 0:10:42–0:10:25  | 0:09:42–0:10:25  |
| [220020068_456241758.mp4](https://vkvideo.ru/video-220020068_456241758)                             | 0:01:58–0:01:02  | 0:00:58–0:01:02  |
| [220020068_456255389.mp4](https://vkvideo.ru/video-220020068_456255389)                             | 0:00:00–0:00:08  | 0:00:06–0:00:08  |

---

Это часть изменений. Остальные указаны в train.csv и test.csv соответственно.

## Подход


1. Предобработка и feature-extractor

    Для каждого видео были извлечены кадры, относящиеся к заставкам, с интервалом в одну секунду.

    Дополнительно выбирались кадры вне заставок: их количество было вдвое больше, чем кадров из заставок, а выбор начинался с кадра, следующего за концом заставки плюс её длительность.

    Все кадры размечались бинарными метками (заставка/не заставка) и использовались для дообучения feature-extractor на задаче бинарной классификации.

2. Формирование представления видео

    Для каждого видео последовательно извлекалось 512 кадров с интервалом в одну секунду.

    Каждый кадр пропускался через feature-extractor, и полученные признаки агрегировались, формируя представление одного видео в виде тензора.

3. Регрессия

    Видео в виде тензоров подавались на вход нейронной сети, которая предсказывает два таргета: момент начала заставки и её длительность.

    Для повышения обобщающей способности в процессе обучения применялись аугментации для извлечённых feature-map : T.RandomHorizontalFlip(p=0.5) и T.RandomRotation(15).

## Архитектура

1. Предобработка и извлечение feature-map
   
    В качестве feature-extractor использовался предобученный ResNet34 с весами ResNet34_Weights.

2. Регрессия - TimeNet
   
   Для решения задачи регресии использовалась кастомная сеть TimeNet.
   <img src="https://github.com/Lexus-FAMCS/VK/blob/master/TimeNet.png" width="500">

P.S. Весь код находится в vk.ipynb

## Результаты
Результаты сохранены в файлах train_results.csv и test_results.csv
