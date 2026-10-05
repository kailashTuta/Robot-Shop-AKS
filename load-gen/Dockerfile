FROM python:3.13-slim-bookworm

# Some default values
ENV HOST="http://localhost:8080/" \
    SILENT=0 \
    NUM_CLIENTS=1 \
    ERROR=0 \
    RUN_TIME=0 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1
    

WORKDIR /load

COPY requirements.txt ./

RUN pip install -r requirements.txt

COPY --chmod=755 entrypoint.sh ./
COPY robot-shop.py ./

CMD ["./entrypoint.sh"]
