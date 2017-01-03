# Django Settings

Define a file that include all settings for configurating Django behaviours

## Global settings

- `DEBUG`: turn on/off debug mode, available options are `True` and `False`
- `ALLOWED_HOSTS`: when site move to production stage, we need to add site's domain to this configuration
- `INSTALLED_APP`: tell Django which applications are active in this project, some default applications are:
	* `django.contrib.admin`: Administration site
	* `django.contrib.auth`: Authentication middleware/framework
	* `django.contrib.contenttypes`: Framework for many content types
	* `django.contrib.sessions`: Session framework for client and server
	* `django.contrib.messages`: Messaging framework, send and receive message between client and server
	* `django.contrib.staticfiles`: Serve and manage static files like images, videos, html, css, js, etc
- `MIDDLEWARE_CLASSES`: a tuple or list contains Django middlewares to be executed
- `ROOT_URLCONF`: a base URL patterns configuration for project
- `TEMPLATES`: a tuple or list contains all project templates engine for serving dynamic contents
- `DATABASES`: a dictionary contains the settings for all the databases to be used in the project. There is always a `default` database
- `LANGUAGE_CODE`: Default language for site
- `LANGUAGES`: a tuple or list contains many tuples declare all available languages for this site, in each tuple, the first element is the code of an language, the second element is display name of the language
- `USE_I18N`: Use international setting
- `USE_L10N`: Use location setting, include number format, date time format
- `USE_TZ`: Use time-zone, if `False` we need to declare site's time-zone in `TIME_ZONE` setting
- `USE_THOUSAND_SEPARATOR`: For number format
- `STATICFILES_DIRS`: extra places for `collectstatic` command to find static files
- `STATIC_ROOT`: define static root folder for serving static files in development
- `STATIC_URL`: url for requesting static files, only useful in development
- `MEDIA_ROOT`: define media root folder for uploaded files
- `MEDIA_URL`: url for requesting media files, only useful in development
