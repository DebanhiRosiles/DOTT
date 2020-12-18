FROM python:3-alpine

WORKDIR /app

COPY $WORKPLACE/python/requirements.txt .
RUN pip install -r requirements.txt

COPY . .
CMD ["ash", "-c", "python api.py"]
#to make the build use:
# docker build -t pym .
# docker run -ti -p 8000:8000 pym
