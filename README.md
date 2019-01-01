# Django-help
Helps in creating website on Django

	Видеоуроки Олег Молчанов
https://www.youtube.com/user/zaemiel/videos

	BOOTSTRAP
https://getbootstrap.com/docs/4.1/layout/grid/
https://www.itwonders-web.com/tools/bootstrap4-editor	# Редактор
https://hackerthemes.com/bootstrap-cheatsheet/		# Шпора КРУУТЬ!

	JQUERY
https://oscarotero.com/jquery/ 	# ШПОРА АГОНЬ!

# УСТАНОВКА И ЗАПУСК DJANGO
#Установка virtualenv для конкретного Python37:
virtualenv venv -p C:\Users\Olezhik\AppData\Local\Programs\Python\Python37\python.exe
	Чтобы virtualenv был транспортируемый
virtualenv --relocatable my-venv

	Активирую virtualenv:
venv\scripts\activate

	Установка Django:
pip install Django

	Создаю проект на Django
	В папке с venv, где empl=имя проекта:
django-admin startproject empl

	Захожу в папку проекта:
cd empl

	Запускаю проект (1 вариант из 3):
python manage.py runserver			# Запустит на http://127.0.0.1:8000/
python manage.py runserver 5000		# Запустит на http://127.0.0.1:5000/
python manage.py runserver 192.168.31.174:5000	# Добавить 192.168.31.174 в settings.py в ALLOWED_HOSTS

	Создаю приложение:
python manage.py startapp tree

	Делаю миграции
python manage.py makemigrations		# Если были сделаны изменения в модели
python manage.py migrate			# Делаю миграции

Нельзя удалять папку migrations (в ней __init__.py), тогда миграции не записываются
Если удалил папку, создай заново + __init__.py (папка с пустым файлом)
# ------------------------------------------ #
# Выгрузка данных из БД Django:
python manage.py dumpdata --indent=2 --exclude=contenttypes > datadump.json

	Загрузка данных в БД:
python manage.py makemigrations
python manage.py migrate
python manage.py loaddata datadump.json
# ------------------------------------------- #

	DEBUG settings DJANGO
# в settings.py свойство 
DEBUG = True

	SETTINGS
https://docs.djangoproject.com/en/2.1/ref/settings/

	Error reporting
https://docs.djangoproject.com/en/2.1/ref/request-response/



# ------------------------------------------- #
	В settings.py в INSTALLED_APPS добавляю:
'tree'		# Название мого приложения

# MY SQL
pip install mysqlclient		# Рекомендовано для Django

Создаю БД 'name' utf8_general_ci		# utf8_general_ci - быстрее работает, utf8_unicode_ci - надежнее ищет

В settings.py в DATABASES пишу
	'default': {
        'ENGINE': 'django.db.backends.mysql',
        'OPTIONS': {
            'read_default_file': 'empl/my.cnf',
			'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
        },
    }
	
	В той-же директории, где settings.py создаю my.cnf:
[client]
database = someapp
user = django
password = PASSWORD
default-character-set = utf8

	Проверяю работу My SQL:
python manage.py makemigrations
python manage.py migrate			# Должны создаться табл в БД
# ---------------------------------------------- #
https://django-extensions.readthedocs.io/en/latest/installation_instructions.html
# СВОЙ СКРИПТ в Django

pip install django-extensions	# Устанавливаю расширение django-extensions

	Добавляю в settings.py в INSTALLED_APPS
'django_extensions',

	В корне, где manage.py, 
scripts 	# создаю папку
__init__.py	# в ней файл
script.py 	# Файл скрипта, который хочу выполнить в Django

	В скрипте: 
def run():		# При запуске скрипта, вызовется эта ф-ция
	....
	
	Запуск скрипта:
python manage.py runscript script
# ------------------------------------------------- #
# ОТКАТ МИГРАЦИИ

	Сначала сохраним все данные из БД см # Выгрузка данных из БД Django
python manage.py migrate <app_name> <migration_name>
python manage.py migrate tree 0005_auto_20181222_1702.py

# ------------------------------------------------- #

# Полная перестройка БД с удаленим данных
reset_db	# требуется django_extensions, см выше

# ------------------------------------------------- #

# ПОИСК В БД
https://docs.djangoproject.com/en/2.1/ref/models/querysets/#delete

https://docs.djangoproject.com/en/2.1/topics/db/aggregation/#filtering-on-annotations

	Про создание и поиск в таблице
https://docs.djangoproject.com/en/dev/topics/db/queries/#limiting-querysets

Entry.objects.all()[5:10]

q1 = Entry.objects.filter(headline__startswith="What")
q2 = q1.exclude(pub_date__gte=datetime.date.today())	# исключает заданные парам.
q3 = q1.filter(pub_date__gte=datetime.date.today())

Modelname.objects.filter(gender='MALE', age__gte = 10, age__lte = 50).all()  # Больше или = 10 и Меньше или = 50

Genre.objects.filter(tree_id=1, level__lte = 1).order_by('-rght').all()

	Кол-во объектов вернет
Genre.objects.count()

Employees.objects.filter(tree_id=1, level__lte=1).order_by('-rght').all()

	Первые 50 объектов, отсортированные
Employees.objects.filter(level__lte=6).order_by('-rght').all()[:50]

Blog.objects.order_by('id').all() - сортирует по полю id

# QuerySet API
	Возвращают объекты...
filter() # соответствующие параметрам поиска
exclude() # не соответствующие параметрам поиска
order_by(*fields) # упорядоченные по полям fields
reverse() # измененные в обратном порядоке
values() # которые содержат словари, а не экземпляры модели
Blog.objects.filter(name__contains='Ch').values('name')
all()
qs1.union(qs2, qs3) # комбинирующ неск QuerySet
qs1.intersection(qs2, qs3) # пересекающиеся в наборах
qs1.difference(qs2, qs3) # которые отличаются в наборах
select_related(*fields) # находит связанные с полем
Entry.objects.get(id='foo') # вернет 1 запись

from django.db.models import Q
Model.objects.filter(Q(x=1) & Q(y=2))	# оператор И
Model.objects.filter(Q(x=1) | Q(y=2))   # или

Entry.objects.count() # Считает число объектов
Entry.objects.latest('pub_date') # последний объект в таблице на основе данного поля
earliest() # первый объект в табл
.first()
.last()
.exists() # вернет True, если в наборе хоть 1 эл-т
.delete() # удалит набор или 1 объект

	Для filter(), exclude() и get()
exact # точное соответств переводит None в 0
Entry.objects.get(id__exact=None)
iexact # регистронезависимое точное сравнение
Blog.objects.get(name__iexact='beatles blog')
contains # содерж с уч регистра
Entry.objects.get(headline__contains='Lennon') 
icontains # не чувствит к регистру
Entry.objects.get(headline__icontains='Lennon')
in # In a given iterable
Entry.objects.filter(id__in=[1, 3, 4])
gt # Больше чем
Entry.objects.filter(id__gt=4) 
gte # больше или равно
lt # меньше чем
lte # меньше или равно
Entry.objects.filter(headline__startswith='Lennon') # регистрозависим
Entry.objects.filter(headline__istartswith='Lennon') # не завис регистр
Entry.objects.filter(headline__endswith='Lennon')
Entry.objects.filter(headline__iendswith='Lennon')

range # в диапазоне
import datetime
start_date = datetime.date(2005, 1, 1)
end_date = datetime.date(2005, 3, 31)
Entry.objects.filter(pub_date__range=(start_date, end_date))

date # для поиска по дате
Entry.objects.filter(pub_date__date=datetime.date(2005, 1, 1))
year
Entry.objects.filter(pub_date__year=2005)
month
Entry.objects.filter(pub_date__month__gte=6)
day
Entry.objects.filter(pub_date__day__gte=3)
week
Entry.objects.filter(pub_date__week__gte=32, pub_date__week__lte=38)
week_day # день недели
Entry.objects.filter(pub_date__week_day__gte=2)
quarter # квартал
Entry.objects.filter(pub_date__quarter=2)
time, hour, minute, second, 
isnull
Entry.objects.filter(pub_date__isnull=True)
regex # чувствителен к регистру
Entry.objects.get(title__regex=r'^(An?|The) +')
iregex # не чувствителен к регистру


# ------------------------------------------------- #

# ОПЕРАТОРЫ ШАБЛОНОВ
		
	Можно and, not, or, and not
	Нельзя одновремено and и or
{% if today_is_weekend %}		
    <p>Welcome to the weekend!</p>
{% else %}						
    <p>Get back to work.</p>
{% endif %}

	Можно вкладывать if в if
{% if athlete_list %}			
    {% if coach_list or cheerleader_list %}
        We have athletes, and either coaches or cheerleaders!
    {% endif %}
{% endif %}

	reversed - обратный отсчет
{% for athlete in athlete_list reversed %}
...
{% endfor %}

	Можно вложенные циклы
{% for athlete in athlete_list %}
    <h1>{{ athlete.name }}</h1>
    <ul>
    {% for sport in athlete.sports_played %}
        <li>{{ sport }}</li>
    {% endfor %}
    </ul>
{% endfor %}

	Проверка возможно пустого списка
{% if athlete_list %}
    {% for athlete in athlete_list %}
        <p>{{ athlete.name }}</p>
    {% endfor %}
{% else %}
    <p>There are no athletes. Only computer programmers.</p>
{% endif %}
	
	Проверка ВОЗМОЖНО ПУСТОГО списка
{% for athlete in athlete_list %}
    <p>{{ athlete.name }}</p>
{% empty %}
    <p>There are no athletes. Only computer programmers.</p>
{% endfor %}

	Счетчик цикла FOR 'forloop.counter' старт с 1
{% for item in todo_list %}
    <p>{{ forloop.counter }}: {{ item }}</p>
{% endfor %}

forloop.counter0 аналогичен с forloop.counter, но его отсчёт начинается с нуля, а не единицы
forloop.revcounter содержит в себе количество оставшихся итераций
forloop.revcounter0 заканчивается на 0
forloop.first – True при первом выполнении цикла
forloop.last – аналогичен forloop.first, но будет True при последнем выполнении цикла
forloop.parentloop – ссылка на родительский цикл в случаях с вложенными циклами, пример: {{ forloop.parentloop.counter }}
forloop допустима только в циклах

	Если равно оператор
{% ifequal section 'sitenews' %}
    <h1>Site News</h1>
{% else %}
    <h1>No News Here</h1>
{% endifequal %}

	Допустимые значения
{% ifequal variable 1 %}
{% ifequal variable 1.23 %}
{% ifequal variable 'foo' %}
{% ifequal variable "foo" %}

	Комментарии:
{# This is a comment #}

	Или так
{% comment %}
This is a
multi-line comment.
{% endcomment %}

	ФИЛЬТРЫ https://docs.djangoproject.com/en/dev/ref/templates/builtins/#ref-templates-builtins-filters
{{ value|capfirst }}  # Первая буква заглавная
{{ name|lower }}	# Перевод в нижний регистр
{{ my_list|first|upper }}	# Первый эл-т списка ЗАГЛАВНЫМИ
{{ bio|truncatewords:"30" }}	# Вывод первых 30 символов или слов
{{ name|addslashes }} – добавляет символы косой черты перед любыми другими слешами, одинарными или двойными кавычками
{{ pub_date|date:"F j, Y" }}	# форматирует дату
{{ myDate | date:"D d M Y"}}   # Wed 20 Jul 2016
{{ name|length }} – возвращает длину значения списка, строки, словаря..
{{ 10|add:15}}                 # 25
{{ "super"|add:"glue" }}       # superglue
# ----------------------------------------------- #

# The 7 Software “-ilities” You Need To Know
1. Usability
Есть ли метафора для моего интерфейса, понятная пользователям? (пример: рабочий стол-метафора)
Часто используемые ф-ции легкодоступны?
Может новый юзер адаптироваться сам? (Интерфейс интуитивен?)
Все ли проверки и сообщения об ошибках нужны?

2. Maintainability ( or Flexibility / Testibility) - ремонтопригодность
Вся ли команда понимает исходный код(алгоритм приложения)?
Весь ли код протестирован?
Могут ли изменения в проекте быть сделаны своевремено?

3. Scalability - масштабируемость(реакция на перегрузку)
Какую пиковую нагрузку может выдержать приложение?
Сколько записей в БД может быть выполнено без сильных тормозов?
Стратегия масштабирования в модернизации узлов или в их наращивании?

4. Availability (or Reliability надежность) - среднее время наработки на отказ (MTBF)
Как долго система может работать без сбоев?
Сколько времени нужно, чтобы отключить систему?
Может ли время отключения быть запланировано?

5. Extensibility - растяжимость
Позволяет ли схема БД приспосабливаться к изменениям?
Фреймворк управляет кодом программиста, или программист управляет фреймворком? Inversion of Control(IoC)
Может ли конечный пользователь расширить систему? (Скрипты, поля ...)
Могут ли сторонние разработчики использовать вашу систему?

6. Security
Безопасность основана на смене пользователей или ролей?
Есть ли необходимость обеспечения безопасности доступа к коду?
Какие операции должны быть обезопашены?
Как пользователи будут администрироваться?

7. Portability - портативность. На скольких  платформах может работать
Можно ли импортировать данные БД на другие системы?
Какие браузеры поддерживает приложение?
Какие операционные системы поддерживаются программой?

# ------------------------------------- #

# АУТЕНТИФИКАЦИЯ ПОЛЬЗОВАТЕЛЯ:
https://docs.djangoproject.com/en/2.1/topics/auth/default/

	Redirect to home URL after login (Default redirects to /accounts/profile/)
LOGIN_REDIRECT_URL = '/'

	Создаю суперпользователя:
manage.py createsuperuser 	# Заполняю поля

	Захожу в админку .../admin - авторизуюсь

	Пишу в шаблоне
{% if request.user.is_authenticated and request.user.is_staff %}
<h2> Вы авторизованный юзер и Вы админ! </h2>
{% endif %}

	Закрыть доступ к странице можно во view.py
	# через декоратор, если функция
@login_required
def view(...):
    ...
	
	или через миксин, если класс
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import TemplateView

class ProtectedView(LoginRequiredMixin, TemplateView):
	raisee_exception = True # Это возвр 403 Forbidden, все ниже не надо
    template_name = 'secret.html'	# Это не влияет
    
#LOGIN_URL = '/accounts/login/' в settings.py # Это не влияет
	Эту ф-цию во views.py
def no_auth(request):
    return render(request, 'no_auth.html')
	Это в urls.py	
path('accounts/login/', no_auth),

# ------------------------------------------- #

# DJANGO MPTT - деревья

https://django-mptt.readthedocs.io/en/latest/install.html

pip install django-mptt

rock = Genre.objects.create(name="Rock")	# Создаю запись
Genre.objects.create(name="Hard Rock", parent=rock)	# Созд ребенка

# -------------------------------------------- #

# AJAX передача файла:

		
# ОРГАНИЗАЦИЯ КОДА в django
https://habr.com/post/213875/		# Толстые модели и жирные менеджеры
		
# -------------------------------------------- #

	Простая работа с картинками django-imagekit
https://github.com/matthewwithanm/django-imagekit

	Работа с ФАЙЛАМИ
https://djbook.ru/examples/33/
