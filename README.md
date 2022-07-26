## Браузеры:

***
WWW - совокупность компьютеров, которые общаются по http.
Интернет - глобальная сеть соединенных между собой компьютеров, которые общаются на базе tcp/ip => www - часть интернета
***

### 1. Что происходит когда мы нажимаем Enter в адрессной строке браузера?
- тайпинг
- парсинг URL(http:/google.com/ = http(протокол) + google.com(хост) + /(ресурс))
- проверка кешей DNS
- запрос к DNS
- установка TCP соединения (most common for HTTP request)  
  TCP соединение устанавливается с помощью процедуры рукопожатия(трехэтапный процесс обмена сообщениями клиента и сервера).
- браузер отправляет HTTP запрос к серверу
- сервер отправляет ответ
- браузер отображает контент(парсинг html и ресурсов)

### 2. Что такое DNS и как он работает?

DNS - база данных, хранящая пары доменных имен и соответствующих им IP-адресов (IP компьютера, который хостит сайт, к которому обращаемся).

Чтобы найти запись в DNS, браузер проверяет последовательно 4 кеша: браузер -> ОС -> роутер -> isp(internet service provider)
Если в кешах нет записи, то браузер совершает запрос к DNS(поиск по нескольким DNS-серверам(уникален для каждой части домена) до тех пор, пока не найдется, либо ошибка)

### 3. Что такое доменное имя?

Доменное имя - человеко-читаемый адрес сайта, состоящий из потенциально бесконечного количества частей, читается справа налево.
Домен содержит минимум три части: root("."), TLD(".com") и n-lvl-domain("google").

Пример: www.info.google.com
root(".")
top-level(com)
second-level(GOOGLE.com)
third-level(INFO.google.com)


### 4. Что такое HTTP? Что содержится в HTTP-запросе? В чем отличие с HTTPS?

http - протокол, описывает взаимодействие м/ду клиентом и сервером.

запрос/ответ состоит из:
-первая строка (METHOD URI HTTP/VERSION -  req, HTTP/1.0 200 OK - res)
-headers
-body

методы http
-get
-post
-head(как get, только заголовки)
-put
-delete

https = http + ssl || http + tls

### 5. Что такое корсы, для чего они нужны и как с ними справляться?

CORS - механизм(браузерный), позволяющий получать ресурсы с другого источника. Источник другой, когда отличен домен или поддомен / простокол / порт.
Благодаря этому достигается безопасность.
Другой источник разрешает доступ с помощью headers(access-control-allow-origin).

Есть два вида CORS-запросов:
простые - GET, POST, HEAD без пользовательских заголовков;
предварительные(с OPTIONS) - все остальные (делают предварительный запрос с целью узнать можно ли сделать основной запрос с определенными методами и заголовками);

OPTIONS-запрос - нужен для описания параметров соединения (методы/заголовки, поддерживаемые сервером).
решение на back: прописать разрешенные источники в обработчики запросов, middleware

### 6. Как парсится html в браузере

1. HTML парсится и разбивается на токены.
2. токены преобразуются в ноды.
3. ноды собираются в DOM дерево
4. параллельно парсится CSS и строится CSSOM
5. CCSOM и DOM объединяются в render tree(скрыты все невидимые элементы и добавлены псевдоэлементы)
6. reflow/layout - браузер проходится по render tree и вычисляет координаты элемента и пространство, которое он занимает.
   процесс запускается каждый раз при изменении стилей, влияющих на положение/размер, также срабатывает при elem.offsetLeft, elem.getBoundingClientRect, скролле.
   происходит в основном потоке, поэтому блок при тяжелых операциях.
7. repaint - браузер обходит layout tree и создает записи о том как будут отрисованы жлементы (пеозиция, цвет и т.д.)
8. composite - группировка элементов по слоям, растрирование в отдельном потоке(composite thread).


### 7. Как грузятся ресурсы (js, css), что такое async и defer
когда браузер загружает html и доходит до тега script он не может продолжать строить DOM, он должен сначала обработать и выполнить скрипт.
проблемы: скрипты не видят DOM-элементы ниже себя, скрипт вверху страницы блокирукет страницу.
помещение скрипты вниз не решит проблему, т.к. при большом html будет задержка

defer: не блокирует страницу и будет выполнен когда dom дерево построено (но до DOMContentLoaded)
async: делает скрипт абсолютно независимым (он сам не ждет выполнения ч-л и ничто не ждет его выполнения)

На практике defer используется для скриптов, которым требуется доступ ко всему DOM и/или важен их относительный порядок выполнения.
А async хорош для независимых скриптов, например счётчиков и рекламы, относительный порядок выполнения которых не играет роли.