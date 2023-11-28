# reserved-tcp-address-edge-ngrok
Create a Reserved Address and Edge in ngrok via API

```

curl --location 'https://api.ngrok.com/reserved_addrs' \
--header "Authorization: Bearer $NGROK_API_KEY" \
--header 'Content-Type: application/json' \
--header 'Ngrok-Version: 2' \
--data '{"description":"Your New TCP Address","region":"us"}' \
-o RESERVEDADDRESS.json

read RESERVEDADDRESS <<< $(jq '.addr' RESERVEDADDRESS.json | tr -d '"')
echo "Reserved Address:$RESERVEDADDRESS"



curl --location 'https://api.ngrok.com/backends/tunnel_group' \
--header "Authorization: Bearer $NGROK_API_KEY" \
--header 'Content-Type: application/json' \
--header 'Ngrok-Version: 2' \
--data '{"description":"acme tunnel group","metadata":"{\"environment\": \"staging\"}","labels":{"baz":"qux","foo":"bar"}}' \
-o TUNNELGROUP.json

read TUNNELGROUP <<< $(jq '.id' TUNNELGROUP.json | tr -d '"')
echo "Create Tunnel Group:$TUNNELGROUP"



curl --location 'https://api.ngrok.com/edges/tcp' \
--header "Authorization: Bearer $NGROK_API_KEY" \
--header 'Content-Type: application/json' \
--header 'Ngrok-Version: 2' \
--data '{
    "description": "acme tcp edge 44",
    "metadata": "{\"environment\": \"staging\"}",
    "hostports":["'"$RESERVEDADDRESS"'"],
    "backend":{"backend_id":"'"$TUNNELGROUP"'", "enabled":true}
}'


```
