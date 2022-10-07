# ops-local-hosting

## Local setup

## Download repository

```
sudo -i
git clone https://github.com/mbayangdiop95/dhis2-local.git
cd dhis2-local
```

### Create a venv in the directory

```
apt install python3-venv
python3 -m venv env
```

### Install the dependencies (ansible and external ansible playbooks)

```
source env/bin/activate
pip install wheel
pip install psycopg2-binary
pip install -r requirements.txt
ansible-galaxy install -r requirements.yml
```


## Set up POSTGRESQL

From the server, you will run the follow command to install and configure postgresql.

```
ansible-playbook playbooks/docker/dhis2/postgres/postgresql.yml 
```

## Restore DB

Then we will provide you with a presigned S3 URL to upload the dump.

And you can follow these steps to restore the dump.

```
curl 'S3_URL' -o <BUCKET_NAME>.dump.gz
gzip -d <BUCKET_NAME>.dump.gz

```

Then you can restore the dump by running:

```
pg_restore \
      --format=c \
      --verbose \
      --no-acl \
      --clean \
      --jobs=6 \
      --no-owner \
      --dbname="<dhis_db_name>" \
      <BUCKET_NAME>.dump.gz
```

## Set up DHIS2

From the server, you will run the follow command to install DHIS2.

```
ansible-playbook playbooks/dhis2.yml
```

Checking the status and logs

```
systemctl status dhis2 
journalctl -u dhis2
```

## Upgrade DHIS2

Check releases notes:

```
https://github.com/dhis2/dhis2-releases/tree/master/releases
```

Change DHIS2 version by modifying image from 2.35.14 to 2.36.12

```
nano playbooks/docker/dhis2/docker-compose.yml

```

Redeploy DHIS2

```
ansible-playbook playbooks/dhis2.yml
```

Restart DHIS2

```
systemctl restart dhis2
```