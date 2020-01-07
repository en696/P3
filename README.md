# P3


<h1>4.1 Wstęp</h1>
<h1>4.2 Cześć praktyczna</h1>
<h1>4.2.1 Dokumentacja techniczna</h1>


<h3>Zastosowane technologie</h3>

<h3>Specyfikacja środowiska</h3>

Klaster kubernetesa znajduje się w google cloud i składa się on z: <br>
1x master kubernetesa <br>
3x slave kubernetesa n1-standard-1 <br>
Region działania klustra kubernetesa w google cloud to europe-west3 <br>
Konfiguracja każdego node n1-standard-1 jest taka sama: <br>
Procesor 1 core Xeon(R) CPU @ 2.30GHz <br>
RAM: 3.75GB <br>
Wersja OS "Linux Container-Optimized OS" możemy system zmienić na ubuntu podczas tworzenia klastra <br>
Dysk 50GB lokalny do każdego noda  <br>
Dysk 100GB wspłudzielony pomiedzy nodami <br>
Każdy node posiada efemeryczny adres  publiczny <br>
Wersja docker engina: Docker version 18.09.7, build 2d0083d <br>
Google cloud load balanser typu: Równoważenie obciążenia TCP na poziomie warstwy 4 dla aplikacji działających w oparciu o protokół TCP/SSL


<h3>Zastosowania oraz eksploatacji projektu</h3>





<h1>4.2.2 Dokumentacja użytkownika</h1>


<h1>4.2.3 Dokumentacja wdrażania</h1>

<h3>Proces wdrożenia klastra kubernetes</h3>
Nalezy posiadać konto google oraz mieć do tego konta podłaczona karte płatnicą.
Tworzymy klaster kubernetesa oraz go konfigurujemy:
Wybieramy opcje Klaster Standardowy

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/Klaster-standardowy.png)


Wybieramy nazwe klastra

Wybierami opcje gdzie chcemy aby działał klaster w Typ lokalizacji wybieramy Region  europe-west3 ponieważ to Frankfurt. czyli jedna z najblizej nam połozona lokalizacja.

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/region.png)


Należy teraz wybrać Liczba węzłów tzn z ilu nodów bedzie składał się nasz klaster ja wybrałem 3.
Wybieramy konfiguracja maszyny n1-standart-1 w konfiguracji zawansowanej zmiejszmy rozmiar dysku do 50 GB.


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/nody-konfiguracja.png)


<h3>Polecenie które utworzy nam klaster kubernetesa</h3>

Nie musimy tworzyć klastra recznie mozemy wykorzystać to polecenie.
Polecenie te należy wpisać w consoli google cloud 

gcloud beta container --project "virtual-tape-250814" clusters create "standard-cluster-2" --region "europe-west3" --no-enable-basic-auth --cluster-version "1.13.11-gke.14" --machine-type "n1-standard-1" --image-type "COS" --disk-type "pd-standard" --disk-size "50" --scopes "https://www.googleapis.com/auth/devstorage.read_only","https://www.googleapis.com/auth/logging.write","https://www.googleapis.com/auth/monitoring","https://www.googleapis.com/auth/servicecontrol","https://www.googleapis.com/auth/service.management.readonly","https://www.googleapis.com/auth/trace.append" --num-nodes "3" --enable-cloud-logging --enable-cloud-monitoring --enable-ip-alias --network "projects/virtual-tape-250814/global/networks/default" --subnetwork "projects/virtual-tape-250814/regions/europe-west3/subnetworks/default" --default-max-pods-per-node "110" --addons HorizontalPodAutoscaling,HttpLoadBalancing --enable-autoupgrade --enable-autorepair

<h3>Proces wdrożenia aplikacji w klastrze kubernetesa</h3>

Logujemy  się do consoli w google cloud która zarzadza klastrem 
Należy pobrać konfiguracje obiektów które chcemy wdrozyć, konfiguracje możemy pobrać za pomocą gita
git clone https://github.com/en696/ProjektP2

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/git-clone.png)

<b>kubectl create namespace projekt</b>


Tworzy namespace aby aplikacja nie działała w defult namespace

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/projekt-namespace.png)


<b>kubectl apply -f traefik-rbac.yaml</b>

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/rbc.png)


<b>kubectl apply -f traefik-ingress-controller.yaml</b>

Tworzy ingres kontroler który zawiera Service i Deployment i znajduje sie w namespace kube-system dla większego bezpieczeństwa aby zwykły użytkownik nie mógł go skasować.
Jako ingres kontrolera uzyłem traefika.
Kontener wystawia dwa porty 80 do usług http oraz 8080 do zarzadzania do dashborda
Automatycznie utworzy nam się usługa load balanser w google cloud 


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/traefik-ingress-controller.png)



<b>kubectl apply -f ui.yaml</b>
Tworzy ingresa oraz service dla dashborda


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/ui.yaml.png)


<b>kubectl apply -f cheese-deployments.yaml</b>

Stworzy 3 obiekty typu deployment prostych aplikacji którzy wystawiaja tylko jedna podstronę
Każdy depolyment zawiera 2 pody.


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/cheese-deployments.yaml.png)





<b>kubectl apply -f cheese-services.yaml</b>

Tworzy obiekty typu service dla obiektów deployment 


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/fcheese-services.yaml.png)


<b>kubectl apply -f cheeses-ingress.yaml</b>

Tworzy obiekt ingres dla obiektów deploymnet


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/cheeses-ingress.yaml.png)


<b>kubectl create namespace jenkins</b>

Tworzy namspace jenkins aby odseparować od siebie aplikacje 




![Diagram](https://github.com/en696/P3/blob/master/Obrazki/jenkins-deploy.yaml.png)




<b>kubectl apply -f jenkins-deploy.yaml</b>

Tworzy obiekt typu deployment , services oraz ingres

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/projekt-namespace.png)





<h1>4.2.4 Proces serwisowania</h1>


<h1>4.2.5 Kosztorys</h1>

<h3> Miesieczny koszt utrzymania ceny na dzień 01.01.2020</h3>

Koszt za load balancer



![Diagram](https://github.com/en696/P3/blob/master/Obrazki/load-cena.png)

Koszt clustra kubernetesa złożonego z trzech maszyn oraz uwzględniłem że cluster będzie działa 24/7
![Diagram](https://github.com/en696/P3/blob/master/Obrazki/node-cena.png)


Za 100 GB wspłodzelonego dysku pomiedzy nodami w klastrze zapłacimy


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/store-cena.png)

Łaczny koszt utrzymania infrastruktury to około USD 99.53 per 1 month 


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/total-cena.png)

<h3>Koszt wdrożenia</h3>

Koszt wdrożenia to około 3-5h pracy x 200 zł per h to około 600-1000zł

<h3>Koszt aktualizacji</h3>
Koszt aktualizacji konfiguracji  klastra jest zalezny od pracochłoności zadania godzina pracy to 200 zł 

<h1>4.2.5 Końcowe testy</h1>
<h1>4.3 Wnioski końcowe</h1>
<h1>4.4 Literatura</h1>
