D13.4 Итоговое задание

В файл settings.py добавлены настройки логирования.
Определены четыре формата записи сообщений:
- format_tier1 включает время, уровень логирования, само сообщение;
- format_tier2 включает время, уровень логирования, само сообщение, путь к источнику события;
- format_tier3 включает время, уровень логирования, само сообщение, путь к источнику события, стэк ошибки;
- format_tier4 включает время, уровень логирования, модуль в котором возникло сообщение, само сообщение;
Определены два фильтра:
- require_debug_true, который пропускает записи только в случае, когда DEBUG = True;
- require_debug_false, который пропускает записи только в случае, когда DEBUG = False;
Определены семь обработчиков:
- debug_console отправляет сообщения уровня DEBUG и выше в консоль;
- warning_console отправляет сообщения уровня WARNING и выше в консоль;
- error_critical_console отправляет сообщения уровней ERROR и CRITICAL в консоль;
- general_file_log сохраняет в файл general.log сообщения уровня INFO и выше;
- error_critical_file_log сохраняет в файл errors.log сообщения уровней ERROR и CRITICAL;
- security_file_log сохраняет в файл security.log сообщения, связанные с безопасностью, уровня INFO и выше;
- mail_admins отправляет на почту администраторам сообщения уровней ERROR и CRITICAL;
Определены шесть регистраторов:
- django отправляет сообщения уровня DEBUG и выше на консоль или в файл general.log;
- django.request сохраняет в файл errors.log и отправляет на почту администраторам сообщения уровней ERROR и CRITICAL;
- django.server сохраняет в файл errors.log и отправляет на почту администраторам сообщения уровней ERROR и CRITICAL;
- django.template сохраняет в файл errors.log сообщения уровней ERROR и CRITICAL;
- django.db.backends сохраняет в файл errors.log сообщения уровней ERROR и CRITICAL;
- django.security сохраняет в файл security.log сообщения, связанные с безопасностью, уровня INFO и выше.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

D7.5. Итоговое задание

1. Произведена установка Redis.
2. Произведена установка пакета Celery.
3. Добавлены настройки в конфигурацию проекта, определюящие использование локального сервера Redis в качестве брокера сообщений для пакета Celery.
4. В файле NewsPaper/NewsPortal/tasks.py добавлена задача send_notifications, которая запускается функцией notify_about_new_post при публикации новости в категории, на которую подписаны пользователи. На почту пользователям приходит письмо со ссылкой на добавленную новость (шаблон post_created_email.html).
5. В файле NewsPaper/NewsPortal/tasks.py добавлена задача send_weekly_notifications, которая осуществляет рассылку списка статей, опубликованных на прошедшей неделе. В файле NewsPaper/NewsPaper/celery.py настроено срабатывание задачи send_weekly_notifications по расписанию (каждый понедельник в 8:00 утра). На почту пользователям приходит письмо со списком статей с ссылками для перехода к ним (шаблон weekly_post.html).

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

D6.7. Итоговый проект

1. Добавлена страница подписки на рассылки о новых публикациях в категориях.
Страница подписки доступна по адресу http://127.0.0.1:8000/subscriptions/. Для реализации данной страницы созданы представления SubscriptionsList, subscribe и unsubsribe. Представления зарегистрированы в urls и позволяют подписываться и отписываться от новостной рассылки по отдельным категориям (шаблон subscriptions.html). Дополнительно создано представление CategoryListView для отображения списка публикаций выбранной категории и возможностью подписаться/отписаться от рассылки в данной категории.
Создана модель Subscriber с внешним ключом на User и Category для хранения подписок пользователей.
При публикации новости в категории, на которую подписаны пользователи, им на почту приходит письмо со ссылкой на добавленную новость (шаблон post_created_email.html). Для получения информации о создании публикации используется сигнал m2m_changed, который вызывается при изменении ManyToManyField в модели.

2. Реализована еженедельная отправка списка статей. Подключено приложение django_apscheduler. Добавлена команда запуска периодических задач. Периодическая задача отправки списка статей настроена на каждую пятницу в 18:00. На почту пользователям приходит письмо со списком статей с ссылками для перехода к ним (шаблон weekly_post.html). В письме отображаются только статьи, опубликованные на прошедшей неделе. 

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

D5.8 Итоговый проект

1. Добавлена форма регистрации на сайт с возможностью зарегистрироваться с помощью почты и пароля или через Yandex-аккаунт. После регистрации, входа или выхода пользователя перенаправляет на страницу с новостями.
Регистрация, вход и выход доступны по ссылкам:
accounts/signup/
accounts/login/
accounts/logout/
Также на панель добавлено выпадающее меню "Авторизация" с ссылками на вышеуказанные страницы, в зависимости от текущего состояния пользователя (авторизован / не авторизован).

2. Настроена проверка у представлений создания, редактирования и удаления новостей и статей.
Представлению создания новости/статьи добавлен миксин PermissionRequiredMixin, а для представлений редактирования и удаления добавлен переопределенный миксин PermissionRequiredMixin - OwnerPermissionRequiredMixin. Данное переопределение выполнено с целью проверки авторства, чтобы исключить возможность редактировать и удалять чужие новости/статьи.
Создана группа authors с правами на создание, изменение и удаление новых записей в разделах «Статьи» и «Новости». 
По умолчанию, все новые пользователи при регистрации попадают в группу common users. Для возможности вступить в группу authors на панель добавлена соответствующая ссылка. Реализация данной ссылки выполнена следующим образом:
- добавлено представление user_promotion;
- переопределен метод get_context_data, осуществляющий проверку является ли текущий пользователь участником группы authors;
- в NewsPortal/urls.py добавлена связь представления user_promotion с маршрутом /promote/;
- в шаблоне default.html добавлена ссылка {% url 'user_promotion' %} с проверкой текущего состояния пользователя (авторизован / не авторизован; является автором / не является автором).

3. Отображение на страницах новостного портала ссылок, отвечающих за добавление, редактирование и удаление новостей/статей, зависит от прав пользователя. При попытке попасть на страницы добавления/редактирования/удаления путем ввода адреса в адресной строке без соответствующих прав, пользователю будет выведена страница с ошибкой 403.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

D4.7. Итоговое задание

1. Добавлен постраничный вывод на /posts/, на одной странице выводится не более 10 страниц. Видны номера ближайших страниц, а также возможен переход к первой или последней странице.

2. Добавлена страница поиска /posts/search/
На странице поиска реализована возможность искать новости по критериям:
- по названию;
- по категории;
- позднее указанной даты.

3. Фильтрацию можно выполнять сразу по нескольким критериям.

4. Добавлены страницы создания, редактирования и удаления новостей и статей.
Страницы располагаются по следующим ссылкам:

/posts/news/create/

/posts/news/<int:pk>/edit/

/posts/news/<int:pk>/delete/

/posts/articles/create/

/posts/articles/<int:pk>/edit/

/posts/articles/<int:pk>/delete/

Если введенный адрес страницы редактирования или удаления новости/статьи не соответствует типу категории (NEWS или ARTICLE) то происходит перенаправление пользователя по корректному адресу. Перенаправление пользователя описано в методах dispatch соответствующих представлений.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

D3.6. Итоговое задание

Была создана страница с адресом /news/, на которой отображается общее количество и список новостей.
Новости на данной странице представлены в виде заголовка, даты публикации и первых 20 символов текста. Новости выводятся в порядке от более свежей к самой старой.

Сделана отдельная страница для отображения детальной информации о новости по адресу /news/<id новости>. Информация о новости представлена в виде её заголовка, полного текста и даты публикации. Также выводится автор и рейтинг новости с указанием её типа (новость/статья), в зависимости от указанной при публикации информации. В нижней части новости отображается список категорий.

Был написан собственный фильтр censor, заменяющий буквы нежелательных слов в заголовках и текстах новостей на символ "*".

Все созданные в ходе работы с модулем страницы наследуются от базового шаблона default.html.
