FROM python:3.8.10-slim-buster

# --allow-releaseinfo-change for cases when stable becomes oldstable
RUN apt-get update --allow-releaseinfo-change \
    && apt-get install -y --no-install-recommends \
        postgresql-client \
        nano \
    && rm -rf /var/lib/apt/lists/*

RUN pip install poetry
RUN poetry config virtualenvs.create false

COPY pyproject.toml ./
COPY poetry.lock ./
RUN poetry install

ENV DJANGO_SETTINGS_MODULE main.settings
ENV DJANGO_APP=main

ENV GUNICORN_CMD_ARGS ""

WORKDIR "/usr/django/app"
CMD ["sh", "-c", "./manage.py migrate && ./manage.py createcachetable && ./manage.py collectstatic --noinput && ./manage.py check --deploy --fail-level=CRITICAL && gunicorn -b 0.0.0.0:8000 -t 600 --workers 1 --threads 20 main.wsgi"]

COPY . /usr/django/app