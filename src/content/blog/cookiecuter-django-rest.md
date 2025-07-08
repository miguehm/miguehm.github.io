---
title: Cookiecutter para proyecto Django REST
author: Miguel Hernández
pubDatetime: 2025-07-08T00:45:00Z
slug: cookiecutter-django-rest
featured: true
draft: false
tags:
  - dev
  - python
  - django
  - api
  - cookiecutter
description: Flujo de trabajo recomendado para proyectos REST con Django
---

Obtener plantilla

```bash
cookiecutter gh:agconti/cookiecutter-django-rest
```

Estructura de archivos creada
```
{{cookiecutter.github_repository_name}}/
├── .travis.yml # integración continua
├── Dockerfile
├── docker-compose.yml
├── README.md
├── manage.py
├── requirements.txt
├── docs/
│   ├── index.md
│   └── api/
│       ├── users.md
│       └── authentication.md
├── {{cookiecutter.app_name}}/
│   ├── __init__.py
│   ├── config/
│   │   ├── __init__.py
│   │   ├── common.py
│   │   ├── local.py
│   │   └── production.py
│   ├── users/
│   │   ├── __init__.py
│   │   ├── models.py
│   │   ├── serializers.py
│   │   ├── test/
│   │   │   └── test_views.py
│   │   └── views.py
│   ├── urls.py
│   └── wsgi.py
├── static/
├── media/
└── ... (otros archivos de configuración y soporte)
```

## Ejemplo de Uso. Autenticación de Usuarios mediante JWT

Usar paquete JWT

```bash
pip install djangorestframework-simplejwt
```

Agregar la autenticación JWT en la canfiguración
```bash
// filepath: {root}/config/common.py
# ...existing code...
REST_FRAMEWORK = {
    # ...otras configuraciones...
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        # 'rest_framework.authentication.TokenAuthentication',  # Opcional: puedes quitar esto si solo usarás JWT
    )
}
# ...existing code...
```

Agregar las rutas de JWT a las URLs
```bash
// filepath: {root}/{app_name}/urls.py
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
)
# ...existing code...
urlpatterns = [
    # ...existing code...
    path('api/v1/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/v1/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    # ...existing code...
]
```
> Considerar remover tambien las rutas relacionadas al endpoint de autenticación antiguo.
