#### Пояснения 
У сети ресторанов доставки "ФорФар" есть множество точек, на которых готовятся заказы для клиентов. 
Каждый клиент хочет вместе с заказом получить чек, содержащий детальную информацию о заказе. 
Сотрудники кухни также хотят чек, чтобы в процессе готовки и упаковки заказа не забыть положить всё что нужно.
Сервис получает информацию о новом заказа, создаёт в БД чеки для всех принтеров точки указанной в заказе и ставит асинхронные задачи на генерацию PDF-файлов для этих чеков. Если у точки нет ни одного принтера - возвращает ошибку. 
Если чеки для данного заказа уже были созданы - возвращает ошибку.
PDF-файлы генерируются  из HTML-шаблон. Имя файла имеет следущий вид <ID заказа>_<тип чека>.pdf (123456_client.pdf). 
Файлы сохраняются в директорию ```media/pdf```
Приложение опрашивает сервис на наличие новых чеков. Опрос происходит по следующему пути: сначала запрашивается список чеков которые уже сгенерированы для конкретного принтера, после скачивается PDF-файл для каждого чека и отправляется на печать.
Предусмотрена админка для обеих моделей, с возможностью фильтровать чеки по принтеру, типу и статусу
Описание доступных API методов находится в файле api.yml (swagger-спецификация). 

#### Технические требования
1) Все необходимые пакеты перечислены в ```requirements.txt```

#### Запуск приложения
1) Установите зависимости из ```requirements.txt```:
    - ```pip install -r requirements.txt```
2) Создайте и запустите docker-контейнеры из ```docker-compose.yml```:
    - ```docker-compose up```
3) После того как все контейнеры запустятся и завершат свою инициализацию, примените все необходимые миграции:
    - ```python manage.py makemigrations```
    - ```python manage.py migrate```
4) Для доступа к панели администратора(в которой можно фильтровать чеки) создайте администратора:
    - ```python manage.py createsuperuser```
5) Примените fixtures:
   - ```python manage.py loaddata api/fixtures/initial_data.json```     
6) Запустите воркеров django-rq:
    - ```python manage.py rqworker default```
7) Запустите приложение:
    - ```python manage.py runserver```
