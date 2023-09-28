# Install django social login

    pip install django django-ninja django-allauth
    
    django-admin startproject django_socail_login


## `django_social_login/settings.py`

    INSTALLED_APPS = [
        ...
        'django.contrib.sites',
        'allauth',
        'allauth.account',
        'allauth.socialaccount',
        'allauth.socialaccount.providers.github'
    ]

    MIDDLEWARE = [
        ...
        'allauth.account.middleware.AccountMiddleware',
    ]

    TEMPLATES = [
        {
            ...
            "DIRS": [str(BASE_DIR.joinpath("templates"))],
            ...
        },
    ]


    AUTHENTICATION_BACKENDS = (
        "allauth.account.auth_backends.AuthenticationBackend",
    )

    SITE_ID = 1
    ACCOUNT_EMAIL_VERIFICATION = 'none'
    LOGIN_REDIRECT_URL = 'home'
    ACCOUNT_LOGOUT_ON_GET = True


## `templates/base.html`

    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <link
          href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css"
          rel="stylesheet"
        />
        <link
          rel="stylesheet"
          href="https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css"
        />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Django Social Login</title>
      </head>
      <body>
        {% block content %} {% endblock content %}
      </body>
    </html>


## `templates/home.html`

    {% extends '_base.html' %} {% load socialaccount %}

    {% block content %}

    <div class="container" style="text-align: center; padding-top: 10%;">
      <h1>Django Social Login</h1>

      <br /><br />

      {% if user.is_authenticated %}
        <h3>Welcome {{ user.username }} !!!</h3>
        <br /><br />
        <a href="{% url 'account_logout' %}" class="btn btn-danger">Logout</a>
      {% else %}
        <!-- GitHub button starts here -->
        <a href="{% provider_login_url 'github' %}" class="btn btn-secondary">
          <i class="fa fa-github fa-fw"></i>
          <span>Login with GitHub</span>
        </a>
        <!-- GitHub button ends here -->
      {% endif %}
    </div>

    {% endblock content %}


## `django_social_login/views.py`

    from django.views.generic import TemplateView


    class Home(TemplateView):
        template_name = "home.html"


## `django_social_login/urls.py`

    from django.contrib import admin
    from django.urls import path, include
    from .views import Home

    urlpatterns = [
        path('admin/', admin.site.urls),

        path('accounts/', include('allauth.urls')),
        path("", Home.as_view(), name="home")
    ]


## migrate

    ./manage.py makemigrations
    ./manage.py migrate

## create `superuser`

    ./manage.py createsuperuser

## create GitHub Provider

navigate to [github.com/settings/applications/new](https://github.com/settings/applications/new) and create a new Application

    Application name: Testing Django Allauth
    Homepage URL: http://127.0.0.1:8000
    Callback URL: http://127.0.0.1:8000/accounts/github/login/callback


## start the application

    ./manage.py runserver

## register the GitHub Provider

navigate to [/admin/socialaccount/socialapp/](http://127.0.0.1:8000/admin/socialaccount/socialapp/) and add a Social Application

    Provider: GitHub
    Provider ID: github
    Client id: [copy from GitHub]
    Secret Key: [copy from GitHub]
    Sites: example.com (if you want to change it the ID in `settings.py` has to match with the DB id ot the site)


## test the login

1. logout of the admin panel
2. navigate to [localhost:8080](http://localhost:8080)
3. select 'Login with GitHub'
