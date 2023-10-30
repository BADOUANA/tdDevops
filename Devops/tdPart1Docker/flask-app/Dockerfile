# specifying our base image
FROM alpine:3.18.4
# install the Python pip package to the alpine linux distribution
RUN apk add --update py-pip
#Install all Python requirements for our app to run
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt
#Copy the files you have created earlier into our image
COPY app.py /usr/src/app/
COPY templates/index.html /usr/src/app/templates/
#Specify the port number which needs to be exposed
EXPOSE 5000
#running the application which is simply
CMD ["python", "/usr/src/app/app.py"]
