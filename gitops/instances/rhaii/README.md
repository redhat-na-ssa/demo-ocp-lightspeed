# RHAII Deployment

## Create Secrets

```sh
# Hugging Face token
oc create secret generic hf-secret \
  --from-literal=HF_TOKEN=<your_huggingface_token> \
  -n rhaii

# API key that clients must present as a bearer token
oc create secret generic vllm-api-key-secret \
  --from-literal=VLLM_API_KEY=$(openssl rand -hex 32) \
  -n rhaii
```

## Test the Endpoint

```sh
export RHAII_HOST=$(oc get route rhaii-vllm -n rhaii -o jsonpath='{.spec.host}')
export RHAII_API_KEY=$(oc get secret vllm-api-key-secret -n rhaii \
  -o jsonpath='{.data.VLLM_API_KEY}' | base64 -d)

# List available models
curl -s https://${RHAII_HOST}/v1/models \
  -H "Authorization: Bearer $RHAII_API_KEY" | jq .

# Send a chat completion request
curl -sS "https://${RHAII_HOST}/v1/chat/completions" \
  -H "Authorization: Bearer ${RHAII_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "'"${MODEL}"'",
    "messages": [{"role": "user", "content": "What is OpenShift?"}],
    "temperature": 0.1,
    "max_tokens": 200
  }' | jq -r '.choices[0].message.content'
```
