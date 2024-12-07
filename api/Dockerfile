# Using official python runtime base image
FROM python:3.10.5-alpine3.16

# Set the application directory
WORKDIR /app

RUN apk add --no-cache entr postgresql-dev musl-dev gcc

# Install our requirements.txt
COPY requirements.txt /app/requirements.txt
RUN pip install -r requirements.txt

# Copy our code from the current folder to /app inside the container
COPY . /app

# Make port 80 available for links and/or publish
EXPOSE 80

# Define our command to be run when launching the container
CMD ["gunicorn", "app:app", "-b", "0.0.0.0:8080", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]
--------------------------------


FROM python:3.9-alpine3.13 AS builder

WORKDIR /app

RUN apk add --no-cache gcc musl-dev postgresql-dev

COPY requirements.txt /app

RUN python -m venv /venv && \
    /venv/bin/pip install --no-cache-dir -r requirements.txt

COPY . /app

FROM python:3.9-alpine3.13

WORKDIR /app

RUN apk add --no-cache libpq

COPY --from=builder /venv /venv

COPY --from=builder /app /app

ENV PATH="/venv/bin:$PATH"

EXPOSE 8080

CMD ["gunicorn", "app:app", "-b", "0.0.0.0:8080"]
