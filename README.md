1. Tworzenie przestrzeni nazw zad5:
```
kubectl create ns zad5
```
2. Tworzenie kwoty o zadanych zasobach. Następnie tworzenia poda z obrazu nginx:
```
kubectl create quota quota5 -n zad5 -o yaml -- --hard=cpu=2,memory=1.5G,pods=10 --dry-run=client > quota5.yaml
```
```
kubectl describe quota -n zad5
```
![Alt text](<Screenshot 2023-11-25 at 17.41.26.png>)
```
kubectl run nginx5 --image=nginx -o yaml -- --dry-run=client > nginx5.yaml
```
3. Zmodyfikowany plik php-apache.yaml:
![Alt text](<Screenshot 2023-11-25 at 17.43.56.png>)

4. Tworzenie pliku hpa (autoscalera). Minimalna liczba replik to 1, a maksymalna 8, dlatego ze limit CPU według kwoty wynosi 2000m, a jeden deployment spozywa do 250m.
```
kubectl autoscale deployment php-apache -o yaml --min=1 --max=8 --cpu-percent=50 --dry-run=client > hpa.yaml
```

![Alt text](<Screenshot 2023-11-25 at 09.35.39.png>)
5. Utworzenie obiektów i potwierzdenie ich utworzenia:
```
kubectl apply -f php-apache.yaml
kubectl get deployment php-apache
kubectl get service php-apache
```
![Alt text](<Screenshot 2023-11-25 at 17.40.33.png>)
6. Generowanie obciązenia dla serwera:
```
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
```
Obserwowanie zmian w skalowaniu za pomocą flagi --watch:
```
kubectl get hpa php-apache --watch
```
![Alt text](<Screenshot 2023-11-25 at 17.36.41.png>)