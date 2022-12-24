---
layout: post
title:  "Интерактивные аналитические панели в Python – руководство по Plotly Dash"
permalink: /plotly-dash-interactive-dashboards/
date:   2022-12-24 23:24:00 +0800
categories: Python
---
Интерактивные дашборды (аналитические панели) идеально подходят для наглядного представления разнообразных данных. В этом уроке по Plotly Dash мы вам покажем, как создавать их с помощью языка Python.
В обязанности специалистов по данным часто входит не только анализ, но и визуальное представление результатов. И очень здорово, если это не просто статический PDF-файл или вручную созданные слайды Powerpoint, а интерактивная аналитическая панель. С таким приложением пользователь может взаимодействовать, просматривая показатели и фильтруя их по своему вкусу. Кроме того, при появлении новых данных приложение обновляется. Если вам приходилось ежемесячно или еженедельно заниматься составлением отчётов в Excel или Powerpoint, вы сразу поймёте, о чём речь.

![](https://raw.githubusercontent.com/evgslesar/evgslesar.github.io/master/_posts/images\plotly-dash-interactive-dashboards\interaktive-dashboards-plotly-dash.jpg)

## Программное обеспечение для создания дашбордов
Для создания панелей мониторинга данных есть ряд предложений как коммерческих, так и бесплатных.
* [Microsoft Power BI](https://powerbi.microsoft.com/de-de/): довольно громоздкая программа, которая больше подходит для бизнес-аналитики, чем для продуманных дашбордов с высоким уровнем интерактивности. Версия для десктопных компьютеров распространяется бесплатно. Однако для того, чтобы с удобством делиться отчётами с другими людьми (не просто пересылая файлы туда-обратно), надо получить лицензию на облачное решение. С другой стороны, многие компании так или иначе платят за офисный пакет, в который входит PowerBI.
* [Qlik или QlikSense](https://www.qlik.com/): Лично на мой вкус это довольно хороший вариант, но лицензия стоит дорого. Обширные возможности настройки с помощью скриптов являются одновременно проклятием и благословением программы. Они позволяют очень гибко управлять данными, но из-за того, что здесь приходится комбинировать навыки программирования и манипуляций мышкой, разобраться с ней непросто.
* [Tableau](https://www.tableau.com/): Эта программа получила много похвальных отзывов в начальный период. Её сильная сторона в основном касается визуализации данных. Но при этом за использование также придётся заплатить.
* [Grafana](https://grafana.com/): отличное бесплатное программное обеспечение, которое используется в основном для отслеживания временных рядов (мониторинг серверов и так далее)

## Программирование аналитических панелей с помощью языка Python
Преимуществом и в то же время недостатком всех решений c графическими интерфейсами является именно визуальная работа. Хотя благодаря этому дашборды могут создавать даже люди, не умеющие программировать. Но мы часто недооцениваем возможности кастомизации, пока не столкнёмся с ситуацией, где они нужны. В сложных проектах часто бывают нужны разные файлы для разных модулей, определений и т.д.
И в таких случаях удобнее использовать полностью программируемые аналитические панели. Преимуществом таких фреймворков является их настраиваемость. Недостатком же является то, что вам нужно обладать некоторыми навыками программирования, а также разбираться в основах HTML/CSS.
Для тех, кто программирует на R, есть фреймворк [Shiny](https://shiny.rstudio.com/) – решение, мимо которого вы не пройдёте. Для Python есть два крупных фреймворка: [Plotly Dash](https://dash.plotly.com/) и [Bokeh](https://bokeh.org/). Популярность Dash неуклонно растёт, при этом освоить его легче. Для работы с Bokeh во многих случаях может понадобиться знание Javascript. И поэтому данное руководство для начинающих мы посвятили Plotly Dash.

## Что такое Plotly Dash?
Dash – это фреймворк программирования веб-приложений для анализа/визуализации данных на языках Python, R или Julia, созданный компанией Plotly. В основе Dash лежит известная Javascript-библиотека React и Flask, один из самых популярных решений для серверной части на языке Python.
Все, что реально сделать с помощью Dash, вы можете посмотреть в [Dash App Galerie](https://dash-gallery.plotly.host/Portal/).
Dash – полностью бесплатный пакет с открытым исходным кодом. Однако компания Plotly предлагает и коммерческие решения для размещения веб-приложений. Сразу проясним один момент: для того, чтобы кто-то другой мог видеть ваш дашборд в браузере, вам нужен сервер с поддержкой Python. В сети есть несколько бесплатных предложений (pythonanywhere, Heroku), которые подойдут для начальных попыток. Если же вы хотите создать несколько приложений, лучше заплатить за WPS. К счастью, это совсем недорого. Например, [Linode](https://linode.gvw92c.net/x06jx) позволяет арендовать небольшую виртуальную машину за 5 долларов в месяц.

## Установка Plotly Dash
Устанавливается библиотека очень просто с помощью либо pip, либо conda.
{% highlight python %}
pip install dash
{% endhighlight %}
или
{% highlight python %}
conda install dash
{% endhighlight %}

## Первый дашборд на основе Plotly Dash
После импорта библиотеки Dash и связанных с ней HTML-функций перейдём к созданию самого приложения. Для начала определим макет страницы, который сейчас состоит только из заголовка H1 "Welcome to Dash!“. После этого у нас запускается сервер в основной подпрограмме.

{% highlight python %}
import dash
import dash_html_components as html
 
app = dash.Dash(__name__)
 
app.layout = html.H1(children="Welcome to Dash!")
 
if __name__ == "__main__":
    app.run_server(debug=True)
{% endhighlight %}

Теперь при запуске этой программы в консоли должно появиться следующее. Таким образом, вы запустили у себя сервер Flask, который доступ через веб-браузер по адресу [http://127.0.0.1:8050](http://127.0.0.1:8050/) (только с вашего компьютера, так как 127.0.0.1 – это адрес локального хоста, то есть самого компьютера)
![](https://raw.githubusercontent.com/evgslesar/evgslesar.github.io/master/_posts/images\plotly-dash-interactive-dashboards\plotly-dash-server.png)
Вот как это будет выглядеть в браузере:
![](https://raw.githubusercontent.com/evgslesar/evgslesar.github.io/master/_posts/images/plotly-dash-interactive-dashboards/screenshot-plotly-dash-tutorial.png)
Пока не особо впечатляет, но тем не менее, у нас уже есть настоящий веб-сайт. Если вы измените что-то в коде и сохраните файл, веб-страница автоматически перезагрузится. Правда иногда она немного подвисает, а затем снова загружается.
Основной макет приложения определяется через app.layout, где можно указать все возможные детали и структуры HTML-кода, а также диаграммы и виджеты. Во втором примере мы его расширим. Никакого функционала пока нет, так как его мы собираемся написать в следующем разделе.

## Элементы управления Plotly Dash, такие как выпадающие списки
Мы будем использовать некоторые элементы как из библиотеки HTML [dash_html_components](https://dash.plotly.com/dash-html-components), так и из основной библиотеки Dash [dash_core_components](https://dash.plotly.com/dash-core-components).
Сначала нам нужно их импортировать. Кроме того, мы создадим график с помощью plotly.express и ряд случайных чисел с помощью numpy.

{% highlight python %}
import dash
import dash_html_components as html
import dash_core_components as dcc
import plotly.express as px
import numpy as np
{% endhighlight %}

Далее мы напишем функцию hist(distribution, n), которая извлекает выборку чисел на основе указанного варианта распределения и делает из неё гистограмму. В качестве параметра функция получает название распределения (normal, binomial или chisquared ) и размер выборки n. Затем с помощью np.random.xxx извлекается случайная выборка и с помощью функции px.histogram plotly express создаётся гистограмма.

{% highlight python %}
def hist(distribution, n):
    data = None
    if distribution == "normal":
        data = np.random.normal(loc=0, scale=1, size=n)
    elif distribution == "binomial":
        data = np.random.binomial(n=10, p=0.5, size=n)
    elif distribution == "chisquared":
        data = np.random.chisquare(df=5, size=n)
    else:
        print("This type of distribution is not supported yet!")
        return
    return px.histogram(data)
{% endhighlight %}

После этого идет блок Dash:

{% highlight python %}
app = dash.Dash(__name__)
 
app.layout = …
 
if __name__ == "__main__":
    app.run_server(debug=True)
{% endhighlight %}

Далее мы определим макет, который, как уже было сказано, состоит из частей dash_html_components и dash_core_components. В принципе, макет представляет собой просто список таких объектов. Кто хоть немного знаком с HTML, узнает эти компоненты: [html.H1](https://dash.plotly.com/dash-html-components/h1) и [html.H2](https://dash.plotly.com/dash-html-components/h2) для заголовков, [html.Div](https://dash.plotly.com/dash-html-components/div) для подразделов. К более сложным элементам относятся [dcc.Dropdown](https://dash.plotly.com/dash-core-components/dropdown) (раскрывающийся список), [dcc.Input](https://dash.plotly.com/dash-core-components/input) (поле ввода) и [dcc.Graph](https://dash.plotly.com/dash-core-components/graph) (график).
Компоненты можно форматировать с помощью CSS и параметра style. При этом в параметр передаётся словарь, где прописываются нужные CSS-стили. Правда синтаксис незначительно изменён, так как использовать дефисы нельзя. То есть, вместо "background-color" нужно писать "backgroundColor".
Имейте в виду, что пока мы просто определили макет страницы. Раскрывающийся список с вариантами распределений и поле ввода размера выборки хотя и отображаются, но ещё не "подключены",то есть, они ничего не делают.
Определение макета выглядит вот так:

{% highlight python %}
app.layout = html.Div(
    children=[
        html.H1(children="Distributions"),
        html.Div(
            children=[
                html.H2("Inputs"),
                html.Div(
                    children=[
                        html.P("Distribution"),
                        dcc.Dropdown(
                            id="distribution-dropdown",
                            options=[
                                {"label": "Normal", "value": "normal"},
                                {"label": "Binomial", "value": "binomial"},
                                {"label": "Chi²", "value": "chisquared"},
                            ],
                            value="normal",
                        ),
                    ],
                ),
                html.Div(
                    children=[
                        html.P("Selection size"),
                        dcc.Input(
                            id="n-input",
                            placeholder="Selection size",
                            type="number",
                            value=100,
                        ),
                    ],
                ),
            ],
            # атрибут style позволяет добавлять форматирование с помощью CSS
            style={
                "backgroundColor": "#DDDDDD",
                "maxWidth": "800px",
                "padding": "10px 20px",
            },
        ),
        html.Div(
            children=[
                html.H2("Histogramm"),
                dcc.Graph(id="histogramm", figure=hist("normal", 100)),
            ],
            style={
                "backgroundColor": "#DDDDDD",
                "maxWidth": "800px",
                "marginTop": "10px",
                "padding": "10px 20px",
            },
        ),
    ]
)
{% endhighlight %}


),

И вот как наша аналитическая панель отображается в браузере:
![](https://raw.githubusercontent.com/evgslesar/evgslesar.github.io/master/_posts/images\plotly-dash-interactive-dashboards\plotly-dash-dashboard-histogramm.png)

## Интерактивная часть панели Plotly Dash
Итак, постепенно мы подошли к самому интересному. Пришло время поговорить об интерактивной части. Она здесь реализована путем добавления декораторов к соответствующим функциям.
При этом, во-первых, определяется выход (Output), то есть какой элемент макета должен быть возвращен функцией. В нашем случае это параметр figure графика dcc.Graph. Для идентификации элементов мы используем параметры id из макета.
Затем нужно определить входные данные (Inputs), то есть элементы, при изменении которых будет вызываться функция. Входных элементов может быть несколько, поэтому они перечисляюся в виде списка. В нашем случае это выпадающий список с идентификатором "distribution-dropdown" и поле ввода с идентификатором "n-input".
Итак, к нашей функции hist просто добавляется декоратор. Для этого еще нужно импортировать два компонента: Input и Output. Строку импорта вставляем в верхнюю часть скрипта, где находятся все остальные  импорты.

{% highlight python %}
from dash.dependencies import Input, Output
 
@app.callback(
    Output("histogramm", "figure"),
    [Input("distribution-dropdown", "value"), Input("n-input", "value")],
)
def hist(distribution, n):
    data = None
    if distribution == "normal":
        data = np.random.normal(loc=0, scale=1, size=n)
    elif distribution == "binomial":
        data = np.random.binomial(n=10, p=0.5, size=n)
    elif distribution == "chisquared":
        data = np.random.chisquare(df=5, size=n)
    return px.histogram(data)
{% endhighlight %}

Внесем небольшое изменение в макет. В функции dcc.Graph мы определили только id, поэтому параметр figure можно убрать. За счет декоратора функция hist в любом случае вызывается при запуске, а так мы избежим ее двойного вызова.

{% highlight python %}
dcc.Graph(id="histogramm")
{% endhighlight %}

И теперь мы можем использовать раскрывающийся список и поле ввода в браузере. При этом график будет сразу изменяться.
![](https://raw.githubusercontent.com/evgslesar/evgslesar.github.io/master/_posts/images\plotly-dash-interactive-dashboards\plotly-dash-dashboard-interaktivitaet.png)

## Развертывание Plotly Dash в облаке
Теперь надо сделать так, чтобы аналитическая панель открывалась не только на нашем компьютере. И каждый раз устанавливать Python с необходимыми библиотеками тоже довольно неудобно. Итак, мы хотим разместить дашборд Plotly в облаке, чтобы можно было легко получать к нему доступ через браузер. Теоретически вы также можете заставить свой собственный компьютер или Raspberry Pi выполнять роль сервера, но тогда он должен будет работать без перерывов. Поэтому облачное решение – более удачный вариант.
К сожалению, процесс "размещения в Интернете" (который также называют развертыванием) не так прост, потому что вам нужно будет создать подходящую среду с необходимыми пакетами и веб-сервером. Веб-сервер, который идёт в комплекте с Dash или Flask, для реальной работы не подходит.
Раньше хорошим бесплатным вариантом для начала был [pythonanywhere](https://www.pythonanywhere.com/). Но сейчас он не актуален, потому что у библиотеки numpy плохая совместимость с веб-сервером uWSGI. А дата-сайентистам почти невозможно отказаться от numpy и pandas.
Второй бесплатный сервис – [Heroku](https://heroku.com/). Но для его использования нужно установить специальное приложение с интерфейсом командной строки и разобраться, что такое git ([у нас есть статья о нем](https://databraineo.com/ki-training-resources/i-git-i-git-was-ist-versionskontrolle/)). То есть, придется ознакомиться с еще одним руководством.
В качестве третьего варианта и хорошей альтернативы AWS я рекомендую [Linode](https://linode.gvw92c.net/x06jx), где за небольшие деньги можно получить виртуальную машину Linux.
Ну вот и все. Удачи вам и радости от изучения бесчисленных возможностей Plotly Dash.
https://databraineo.com/ki-training-resources/python/interaktive-dashboards-in-python-plotly-dash-tutorial/