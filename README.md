# Классификация токсичных комментариев
### В данном проекте проведен анализ реальных комментариев со страниц обсуждений Википедии. К сожалению, некоторые пользователи позволяют себе писать сообщения, содержащие ругательства, угрозы и оскорбления. Автор данного проекта осуждает этих людей и анализирует токсичные комментарии только с целью построения алгоритма, позволяющего отделять их от чистых комментариев.

### Автор данного проекта не имеет никакого отношения к содержанию анализируемых комментариев. Пожалуйста закройте данный проект, если вы не приемлете сообщения, содержащие ругательства, угрозы, оскорбления и прочие негативные формы выражения своих мыслей.

### Постановка задачи
Задано 312735 реальных комментариев со страниц обсуждений Википедии и 6 классов токсичности комментариев:
* toxic - обычный токсичный
* severe toxic - сильно токсичный
* obscene - непристойный
* threat - содержащий угрозу
* insult - оскорбительный
* identity hate - содержащий ненависть к личности

Необходимо решить задачу классификации: для каждого комментария научиться предсказывать, с какими вероятностями к каким классам токсичности он относится. Комментарий может как не относиться ни к одному классу токсичности, так и относится к одному или нескольким классам токсичности.

### Обработка данных
Данные загруженны со [страницы соревнования на Kaggle](https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge). Обучающая выборка состоит из 159571 объекта, а тестовая - из 153164 объектов. Классы не сбалансированы - менее 11% комментариев из обучающей выборки относятся хотя бы к одному из классов токсичности. Для каждого комментария из тестовой выборки предсказывается 6 целевых переменных: вероятности отнесения комментария к соответствующему классу токсичности.

### Обработка текстов комментариев
Длина комментария оказались неинформативным признаком, снижающим качество модели, поэтому она не используется в итоговой модели.

Текст каждого комментария разбивается на отдельные слова регулярным выражением, после чего происходит [лемматизация](http://www.nltk.org/api/nltk.stem.html#nltk.stem.wordnet.WordNetLemmatizer) с помощью соответствующих инструментов из библиотеки [NLTK](https://www.nltk.org/). В результате каждый комментарий преобразуется в список слов. После этого удаляются стоп-слова: все слова из одной буквы, а также стоп-слова из [списка библиотеки NLTK](http://www.nltk.org/book/ch02.html#code-unusual) и [списка библиотеки Wordcloud](https://github.com/amueller/word_cloud/blob/master/wordcloud/stopwords).

### Анализ текстов комментариев
Для токсичных, чистых и всех комментариев из обучающей выборки с помощью инструментов билиотеки [NLTK](http://www.nltk.org/index.html):
* Подсчитано количество уникальных слов
* Найдены самые популярные слова
* Построено распределение частоты слов, соответствующее [Закону Ципфа](https://ru.wikipedia.org/wiki/%D0%97%D0%B0%D0%BA%D0%BE%D0%BD_%D0%A6%D0%B8%D0%BF%D1%84%D0%B0)
* Найдены самые популярные биграммы и триграммы

Кроме того, построены облака слов для чистых и токсичных комментариев с помощью библиотеки [WordCloud](https://amueller.github.io/word_cloud/generated/wordcloud.WordCloud.html)

### Построение модели
Каждый комментарий преобразован к списку слов. Для создания матрицы признаков объектов используется [TF-IDF](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html) из библиотеки [Scikit-learn](http://scikit-learn.org/stable/index.html). Подобранные параметры: min_df=6, max_df=1.0.

Для предсказания каждой из 6 целевых переменных используется [логистическая регрессия](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html) из библиотеки [Scikit-learn](http://scikit-learn.org/stable/index.html). Во всех случаях оптимальными оказались параметры: C=1.0, penalty='l2'. Метрикой качества является среднее значение площади под ROC-кривой по 6 предсказанным целевым переменным. Итоговое значение метрики: 0.9751.
