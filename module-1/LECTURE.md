 
## 

Create network
```bash
docker network create pg-network
```

PostgreSQL container 
 ```bash
docker run -it \
    -e POSTGRES_USER="root" \
    -e POSTGRES_PASSWORD="root" \
    -e POSTGRES_DB="ny_taxi" \
    -v ./ny_taxi_postgres_data:/var/lib/postgresql/data \
    -p 5433:5432 \
    --network=pg-network \
    --name pg-database \
    postgres:17
```

PGAdmin container
```bash
docker run -it \
    -e PGADMIN_DEFAULT_EMAIL="pgadmin@pgadmin.com" \
    -e PGADMIN_DEFAULT_PASSWORD="root" \
    -p 8080:80 \
    --network=pg-network \
    --name pgadmin \
    dpage/pgadmin4
```

```bash
sudo apt install python3-venv
python3 -m venv venv
. venv/bin/activate
pip install pgcli
pgcli -h localhost -p 5433 -u root -d ny_taxi

pip install notebook
jupyter notebook

wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz

gzip -dk yellow_tripdata_2021-01.csv.gz

less yellow_tripdata_2021-01.csv

count lines 

wc -l yellow_tripdata_2021-01.csv

jupyter nbconvert --to=script upload-data.ipynb

pip install pandas sqlalchemy psycopg2-binary
```


URL="https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

python ingest_data.py \
    --user=root \
    --password=root \
    --host=localhost \
    --port=5433 \
    --db=ny_taxi \
    --table_name=yellow_taxi_trips \
    --url=${URL}



docker build -t taxi_ingest:v001 .

docker run -it \
    --network=pg-network \
    taxi_ingest:v001 \
    --user=root \
    --password=root \
    --host=localhost \
    --port=5432 \
    --db=ny_taxi \
    --table_name=yellow_taxi_trips \
    --url=${URL}



python -m http.server
Directory listing for /
localhost:8000 




python ingest_data_green.py \
    --user=root \
    --password=root \
    --host=localhost \
    --port=5433 \
    --db=ny_taxi \
    --table_name=green_taxi_trips \
    --url=${URL}