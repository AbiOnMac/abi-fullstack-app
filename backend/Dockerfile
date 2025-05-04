FROM python:3.13.3-alpine3.21 AS builder

# Install necessary dependencies
RUN apk add --no-cache gcc musl-dev libffi-dev

# Set working directory for installation
WORKDIR /install

# Copy the requirements file and install dependencies
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

# ----------------------------
# Final Stage
# ----------------------------
FROM python:3.13.3-alpine3.21 AS fastapi-app

# Set the working directory
WORKDIR /app

# Copy the installed dependencies from the builder
COPY --from=builder /root/.local /root/.local

# Copy the FastAPI app directory into the container
COPY ./app /app

# Set environment variables (optional)
ENV PATH=/root/.local/bin:$PATH

# Expose the port that FastAPI runs on
EXPOSE 8000

# Run FastAPI using Uvicorn
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
