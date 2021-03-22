- Criando o cluster no GCE
  > gcloud container clusters create descomplicando-istio 

- Pegando as credenciais do cluster
  > gcloud container clusters get-credentials descomplicando-istio

- Deletando o cluster no GCE
  > gcloud container clusters delete descomplicando-istio

1. Instalando o Istio
  > curl -L https://istio.io/downloadIstio | sh -
  > export PATH=$PWD/bin:$PATH
  > istioctl install || istioctl install --set profile=demo

2. Habilitando o Istio SideCar no namespace default
  > kubectl label namespace default istio-injection=enabled
  
3. Exemplo de aplicação:
  > kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml

4. Abrindo a aplicação para tráfico externo, criando um Istio Ingress Gateway
  > kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml

5. Verificando o ingress IP e Port
  > kubectl get svc istio-ingressgateway -n istio-system

6. Caso tiver acesso a um load balance externo:
  > export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
  > export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
  > export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')

7. Link de acesso a aplicaçao:
  > echo "http://$GATEWAY_URL/productpage"

8. Dashboard Kiali e outros addons:
  > kubectl apply -f samples/addons
  > kubectl rollout status deployment/kiali -n istio-system

9. Expondo o dashboard:
  > kubectl port-forward svc/kiali 20001:20001 -n istio-system --address 0.0.0.0

