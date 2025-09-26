# Install Argo Events CRDs
kubectl apply  -n argo -f 01_install.yaml

# Install with a validating admission controller if needed 
kubectl apply -n argo  -f 02-install-validating-webhook.yaml


# Create the eventbus.
kubectl apply -n argo -f 03-eventbus-native.yaml

# Setup event-source for webhook 
kubectl apply -n argo -f 04-eventsource_webhook.yaml

# Create a service account with RBAC settings to allow the sensor to trigger workflows, and allow workflows to function.
kubectl apply -n argo -f 05-sensor-rbac.yaml

kubectl apply -n argo -f 06-workflow-rbac.yaml

# Create webhook sensor.
kubectl apply -n argo -f 07-sensor_webhook.yaml

# the following steps is to valide
# Expose the event-source pod via Ingress
kubectl -n argo port-forward svc/webhook-eventsource-svc 12000:12000

# Use either Curl or Postman to send a post request to the http://localhost:12000/example.
curl -d '{"message":"this is my first webhook"}' -H "Content-Type: application/json" -X POST http://localhost:12000/example


# Verify that an Argo workflow was triggered and show 'Succeeded' status.
kubectl -n argo get workflows | grep "webhook"
