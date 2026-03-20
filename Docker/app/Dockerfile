# ====================
# Stage 1: python-base (các env chung)
# ====================
FROM python:3.12-slim-bookworm AS python-base

# Env chung cho Python & Poetry
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=off \
    PIP_DISABLE_PIP_VERSION_CHECK=on \
    PIP_DEFAULT_TIMEOUT=100 \
    POETRY_VERSION=1.8.3 \
    POETRY_HOME="/opt/poetry" \
    POETRY_VIRTUALENVS_IN_PROJECT=true \
    POETRY_NO_INTERACTION=1 \
    PYSETUP_PATH="/app" \
    VENV_PATH="/app/.venv"

# Prepend poetry và venv vào PATH
ENV PATH="$POETRY_HOME/bin:$VENV_PATH/bin:$PATH"


# ====================
# Stage 2: poetry-base (cài Poetry)
# ====================
FROM python-base AS poetry-base

RUN apt-get update && apt-get install --no-install-recommends -y curl && \
    curl -sSL https://install.python-poetry.org | python - && \
    apt-get remove -y curl && apt-get autoremove -y && rm -rf /var/lib/apt/lists/*


# ====================
# Stage 3: builder (cài dependencies production)
# ====================
FROM poetry-base AS builder

WORKDIR $PYSETUP_PATH

# Copy chỉ files cần để cache layer deps
COPY pyproject.toml poetry.lock ./

# Cài chỉ production deps (main group), không install project (--no-root), sync sạch
RUN poetry install --only main --no-root --sync


# ====================
# Stage 4: production (runtime image nhẹ)
# ====================
FROM python-base AS production

# Copy venv từ builder (đã có deps production)
COPY --from=builder $PYSETUP_PATH/.venv $PYSETUP_PATH/.venv

# Copy toàn bộ source code
COPY . $PYSETUP_PATH

WORKDIR $PYSETUP_PATH

# Optional: cài thêm runtime deps nếu cần (ví dụ libpq cho PostgreSQL)
RUN apt-get update && apt-get install --no-install-recommends -y \
    libpq5 && \
    rm -rf /var/lib/apt/lists/*

# Chạy với uvicorn (hoặc thay bằng gunicorn cho prod thực thụ)
# Gợi ý: dùng gunicorn + uvicorn worker cho production
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "4"]

# Nếu dùng gunicorn (khuyến nghị prod):
# CMD ["gunicorn", "-k", "uvicorn.workers.UvicornWorker", "-w", "4", "-b", "0.0.0.0:8000", "main:app"]