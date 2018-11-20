##gunicorn gevent worker + django + PyMySQL

* dead lock with preload_app config
  * https://forum.garenanow.com/d/331-deadlock-of-running-django-by-gunicorn-and-gevent
* sync worker with MySQLdb
* cannot reuse db connection
  * https://github.com/benoitc/gunicorn/issues/996
  * https://serverfault.com/questions/635100/django-conn-max-age-persists-connections-but-doesnt-reuse-them-with-postgresq
  * https://forum.garenanow.com/d/62-django-app-in-gunicorn-gevent-mode-will-not-reuse-the-db-connection

* manage.py runserver issue

