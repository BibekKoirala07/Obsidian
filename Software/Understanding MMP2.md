### base
fastapi==0.108.0

uvicorn==0.25.0

pydantic

pydantic-settings

email-validator

python-multipart

sqlalchemy

alembic

python-dotenv

zeep

sqlalchemy_json

boto3

python-jose[cryptography]

bcrypt

psycopg[binary,pool]

psycopg2-binary

openpyxl

google_api_core==2.19.0

firebase-admin==6.5.0

sentry-sdk[fastapi]

pycryptodomex

qrcode

jinja2

grpcio

grpcio-tools

redis

pyotp

confluent-kafka

dogpile-cache

dill

uuid7==0.1.0

defusedxml

httpx==0.27.0

opentelemetry-api

opentelemetry-exporter-otlp

pymssql==2.3.2

granian

elastic-apm
### ci
-r base.txt

pytest

pytest-env

coverage

pytest-cov

pytest-grpc

freezegun

httpx

faker

mypy==1.10.0

pytest-postgresql

psycopg # required by pytest-postgresql

pytest-xdist

requests-mock

### dev
-r base.txt

ipdb

fastapi-debug-toolbar==0.4.0

faker~=22.0.0

freezegun~=1.4.0

httpx==0.27.0

itsdangerous

pre-commit

ruff

typer[all]~=0.9.0

rich~=13.7.0

mypy==1.10.0

types-requests

types-psycopg2

types-openpyxl

types-protobuf

types-python-dateutil

types-qrcode

types-boto3

grpc-stubs

types-python-jose

types-confluent-kafka

libcst
### frozen
alembic==1.15.2

annotated-types==0.7.0

anyio==4.9.0

attrs==25.3.0

backoff==2.2.1

bcrypt==4.2.1

boto3==1.38.19

botocore==1.38.19

cachecontrol==0.14.3

cachetools==5.5.2

certifi==2025.11.12

cffi==1.17.1

charset-normalizer==3.4.2

click==8.2.0

confluent-kafka==2.10.0

cryptography==43.0.3

decorator==5.2.1

defusedxml==0.7.1

deprecated==1.2.18

dill==0.4.0

dnspython==2.7.0

dogpile-cache==1.4.0

ecdsa==0.19.1

ecs-logging==2.2.0

elastic-apm==6.23.0

email-validator==2.2.0

et-xmlfile==2.0.0

fastapi==0.108.0

firebase-admin==6.5.0

google-api-core==2.19.0

google-api-python-client==2.169.0

google-auth==2.40.1

google-auth-httplib2==0.2.0

google-cloud-core==2.4.3

google-cloud-firestore==2.20.2

google-cloud-storage==3.1.0

google-crc32c==1.7.1

google-resumable-media==2.7.2

googleapis-common-protos==1.70.0

granian==2.3.0

greenlet==3.2.2

grpcio==1.71.0

grpcio-status==1.62.3

grpcio-tools==1.62.3

gunicorn==23.0.0

h11==0.16.0

httpcore==1.0.9

httplib2==0.22.0

httptools==0.6.4

httpx==0.27.0

idna==3.10

importlib-metadata==8.6.1

isodate==0.7.2

jinja2==3.1.6

jmespath==1.0.1

lxml==5.4.0

mako==1.3.10

markupsafe==3.0.2

msgpack==1.1.0

openpyxl==3.1.5

opentelemetry-api==1.33.1

opentelemetry-exporter-otlp==1.15.0

opentelemetry-exporter-otlp-proto-grpc==1.15.0

opentelemetry-exporter-otlp-proto-http==1.15.0

opentelemetry-proto==1.15.0

opentelemetry-sdk==1.33.1

opentelemetry-semantic-conventions==0.54b1

packaging==25.0

pbr==6.1.1

platformdirs==4.3.8

proto-plus==1.26.1

protobuf==4.25.7

psycopg==3.2.9

psycopg-binary==3.2.9

psycopg-pool==3.2.6

psycopg2-binary==2.9.10

pyasn1==0.4.8

pyasn1-modules==0.4.1

pycparser==2.22

pycryptodomex==3.23.0

pydantic==2.11.4

pydantic-core==2.33.2

pydantic-settings==2.9.1

pyjwt==2.10.1

pymssql==2.3.2

pyotp==2.9.0

pyparsing==3.2.3

python-dateutil==2.9.0.post0

python-dotenv==1.1.0

python-jose==3.4.0

python-multipart==0.0.20

pytz==2025.2

qrcode==8.2

redis==6.1.0

requests==2.32.3

requests-file==2.1.0

requests-toolbelt==1.0.0

rsa==4.9.1

s3transfer==0.12.0

sentry-sdk==2.47.0

setuptools==80.7.1

six==1.17.0

sniffio==1.3.1

sqlalchemy==2.0.41

sqlalchemy-json==0.7.0

starlette==0.32.0.post1

stevedore==5.4.1

typing-extensions==4.13.2

typing-inspection==0.4.0

uritemplate==4.1.1

urllib3==2.6.1

uuid7==0.1.0

uvicorn==0.25.0

uvloop==0.21.0

wrapt==1.17.2

zeep==4.3.1

zipp==3.21.0
###### DONOT UPGRADE `cryptorgraphy` and `bcrypt` package
###### BREAKS CRONICLE JOBS===

### prod.txt
-r base.txt

gunicorn

bcrypt

uvloop

httptools