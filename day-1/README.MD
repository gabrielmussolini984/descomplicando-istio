- Criando o cluster no GCE
  > gcloud container clusters create primeiro-deployment

- Pegando as credenciais do cluster
  > gcloud container clusters get-credentials primeiro-deployment

- Deletando o cluster no GCE
  > gcloud container clusters delete primeiro-deployment

1. Instalando o Istio:
  > curl -L https://istio.io/downloadIstio | sh -
  > export PATH=$PWD/bin:$PATH
  > istioctl install || istioctl install --set profile=demo

2. Crie um namespace que será escutado pelo istio
  > kubeclt create namespace development

3. Habilitando o Istio SideCar no namespace development
  > kubectl label namespace development istio-injection=enabled

4. Vamos dar um apply em nossos deployments e services: bookinfo.yml
  > kubectl apply -f bookinfo.yml -n development

5. Ver os pods e os services do namespace development
  > kubectl get po -n development
  > kubectl get svc -n development

6. Editando o service productpage para o tipo loadbalance
  > kubectl edit svc productpage -n development

6. Ou podemos expor a porta do serviço com port forward
  > kubectl port-forward svc/productpage 9080:9080 -n development --address=0.0.0.0

7. Vamos dar um apply em nosso gateway e virtual services: gateway.yml (deixar os services em ClusterIP)
  > kubectl apply -f gateway.yml -n development

8. Instalando o Kiali, Prometheus, Jaeger e Grafana
  > kubectl apply -f addons/

9. Expondo o Kiali
  > kubectl port-forward svc/kiali 20001:20001 -n istio-system --address=0.0.0.0

10. Expondo o Grafana
  > kubectl port-forward svc/grafana 3000:3000 -n istio-system --address=0.0.0.0


- Fim do primeiro dia.
- Inicio do segudo dia.

11. Vamos remover o gateway já criado anteriormente:
  > kubectl delete gateways.networking.istio.io bookinfo-gateway -n development

12. Vamos remover o virtual service já criado anteriormente:
  > kubectl delete virtualservices.networking.istio.io bookinfo -n development

13. Vamos criar nossos destinations rulers destination-rule-all.yaml
  > kubectl apply -f destination-rule-all.yaml -n development

14. Vamos criar nossos virtual services virtual-service-all-v1.yaml
  > kubectl apply -f virtual-service-all-v1.yaml -n development

15. Vamos expor nosso service
  > kubectl port-forward svc/productpage 9080:9080 -n development --address=0.0.0.0
  
  - Dependendo de como está configurado nosso virtual services, conseguimos configurar os routes

# Definindo porcentagem de acessos aos services.

16. Delete o virtual service anteriormente criado
  > kubectl delete -f virtual-service-all-v1.yaml -n development

17.  Aplique novamente o mesmo virtual service, passo necessário para resetar qualquer edição feita.
  > kubectl apply -f virtual-service-all-v1.yaml -n development
  
18. O arquivo que define a porcentagem de acesso aos reviews está em virtual-service-reviews-50-v3
  - Isso atualizará o service de review já criado anteriormente
  > kubectl apply -f virtual-service-reviews-50-v3 -n development
   
19. Vamos expor nosso service
  > kubectl port-forward svc/productpage 9080:9080 -n development --address=0.0.0.0
  
