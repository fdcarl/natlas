FROM python:3.6.0 as build

WORKDIR /opt/natlas/natlas-server
COPY requirements.txt /opt/natlas/natlas-server

RUN pip3 --disable-pip-version-check --no-cache-dir install --root=/install -r requirements.txt \
	&& rm -rf requirements.txt

COPY . /opt/natlas/natlas-server

RUN python3 -m compileall .

# Build final image
FROM python:3.6.0-slim

RUN export DEBIAN_FRONTEND=noninteractive \
	&& apt-get update \
	&& apt-get install --no-install-recommends -qy libsqlite3-0

COPY --from=build /install /
COPY --from=build /opt/natlas /opt/natlas

WORKDIR /opt/natlas/natlas-server/
VOLUME ["/data"]
ENV SQLALCHEMY_DATABASE_URI="sqlite:////data/db/metadata.db"
ENV MEDIA_DIRECTORY="/data/media/"
ENV FLASK_APP=./natlas-server.py
ENV FLASK_ENV=production
ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8
EXPOSE 5000
ENTRYPOINT ["/bin/bash", "entrypoint.sh"]

CMD ["/usr/local/bin/gunicorn", "-b", "0.0.0.0:5000", "natlas-server:app"]