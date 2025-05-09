FROM ubuntu:latest
RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install -y python3 python3-pip
# In case of "externally managed" error
RUN rm -f /usr/lib/python*/EXTERNALLY-MANAGED || true
RUN pip3 install flask
WORKDIR /app
COPY ./api.py /app/api.py
CMD ["python3", "api.py"]
