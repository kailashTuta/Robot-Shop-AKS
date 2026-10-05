FROM python:3.13-slim-bookworm

EXPOSE 8080

ENV INSTANA_SERVICE_NAME=payment \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

WORKDIR /app

RUN apt-get update \
    && apt-get install -y --no-install-recommends build-essential curl \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt ./

RUN pip install -r requirements.txt \
    && apt-get purge -y --auto-remove build-essential

COPY *.py payment.ini ./

CMD ["uwsgi", "--ini", "payment.ini"]
