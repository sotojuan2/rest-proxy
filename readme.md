# Pruebas Rest Proxy

## Producir con v2

1. Crear topico en la consola "purchases"
2. Producir datos
   ```bash
   curl -X POST  -H "Content-Type: application/vnd.kafka.json.v2+json"  -H "Accept: application/vnd.kafka.v2+json"  --data '{"records":[{"key":"jsmith","value":"alarm clock"},{"key":"htanaka","value":"batteries"},{"key":"awalther","value":"bookshelves"}]}'  "http://localhost:8082/topics/purchases"
   ```

## Consumir evento V2

1. Crea un consumer group
2. Subscribirte al topico purchases.
3. Consumir. Primero tiene que hacer un join group

```bash
curl -X POST \
     -H "Content-Type: application/vnd.kafka.v2+json" \
     --data '{"topics":["purchases"]}' \
     http://localhost:8082/consumers/cg1/instances/ci1/subscription 
```

4. Consumir de **verdad** (sleep 10 seg)
```bash
sleep 10
curl -X POST \
     -H "Content-Type: application/vnd.kafka.v2+json" \
     --data '{"topics":["purchases"]}' \
     http://localhost:8082/consumers/cg1/instances/ci1/subscription 
```
