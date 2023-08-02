# Rest Proxy

## Produce v2

1. Crear topic "purchases" via UI
2. Produce data
   ```bash
   curl -X POST  -H "Content-Type: application/vnd.kafka.json.v2+json"  -H "Accept: application/vnd.kafka.v2+json"  --data '{"records":[{"key":"jsmith","value":"alarm clock"},{"key":"htanaka","value":"batteries"},{"key":"awalther","value":"bookshelves"}]}'  "http://localhost:8082/topics/purchases"
   ```

## Consume Events V2

1. Create a consumer

   ```bash
   curl -X POST -H "Content-Type: application/vnd.kafka.v2+json" --data '{"name": "ci1", "format": "json", "auto.offset.reset": "earliest"}' http://localhost:8082/consumers/cg1
   ```
2. Subscribe to the topic purchases.
```bash
   curl -X POST
   -H "Content-Type: application/vnd.kafka.v2+json"
   --data '{"topics":["purchases"]}'
   http://localhost:8082/consumers/cg1/instances/ci1/subscription
```
3. Consume

```bash
curl -X POST \
     -H "Content-Type: application/vnd.kafka.v2+json" \
     --data '{"topics":["purchases"]}' \
     http://localhost:8082/consumers/cg1/instances/ci1/subscription 
```

4. Consume  **after 10 seconds** (sleep 10 seg)

```bash
sleep 10
curl -X POST \
     -H "Content-Type: application/vnd.kafka.v2+json" \
     --data '{"topics":["purchases"]}' \
     http://localhost:8082/consumers/cg1/instances/ci1/subscription 
```

5. Close the consumer with a DELETE 

```bash
curl -X DELETE \
     -H "Content-Type: application/vnd.kafka.v2+json" \
     http://localhost:8082/consumers/cg1/instances/ci1 
```

## Produce Events [v3](https://docs.confluent.io/platform/current/kafka-rest/api.html?_gl=1*1mjeavi*_ga*NjY4OTE1OTc1LjE2NzQ4MTg1ODc.*_ga_D2D3EGKSGD*MTY5MDk3Mjg0Mi40NDkuMC4xNjkwOTcyODQyLjYwLjAuMA..&_ga=2.94927900.1215090025.1690790538-668915975.1674818587&_gac=1.120081146.1689856381.CjwKCAjwtuOlBhBREiwA7agf1gCgsIyen7F2dRsqY1Tbatvz_FAfBgkd_O0La_LZIvnPA_tO-bfV8xoCZ5cQAvD_BwE#records-v3)

1. Crear topic "donuts"
2. Get cluster id

   ```bash
   cluster_id=$(curl  -s --location 'http://localhost:8082/v3/clusters' | jq '.data[0].cluster_id' | tr -d '"')
   ```
3. Create schema donust-value

```bash
curl -v -X POST -H "Content-Type: application/json" --data @data/test.avro http://localhost:8081/subjects/donuts-value/versions
```

3. Produce Events (Avro, schema_id & headers)

```bash
curl --location "http://localhost:8082/v3/clusters/${cluster_id}/topics/donuts/records" \
--header 'Content-Type: application/json' \
--data '{
   "key": {
       "type": "BINARY",
       "data": "Zm9vYmFy"
   },
   "headers": [
       {
           "name": "Header-1",
           "value": "SGVhZGVyLTE="
       },
       {
           "name": "Header-2",
           "value": "SGVhZGVyLTI="
       }
   ],
   "value": {
       "schema_version": 1,
       "data": {"field1": "hola", "field2": 4}
   },
   "timestamp": "2021-02-05T19:14:42Z"
}
{
   "key": {
       "type": "BINARY",
       "data": "Zm9vYmFy"
   },
   "headers": [
       {
           "name": "Header-1",
           "value": "SGVhZGVyLTE="
       },
       {
           "name": "Header-2",
           "value": "SGVhZGVyLTI="
       }
   ],
   "value": {
       "schema_version": 1,
       "data": {"field1": "hola1", "field2": 5}
   },
   "timestamp": "2021-02-05T19:14:42Z"
}
'
```
