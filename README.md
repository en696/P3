# P3


<h1>4.1 Wstęp</h1>
<h1>4.2 Cześć praktyczna</h1>
<h1>4.2.1 Dokumentacja techniczna</h1>


<h3>Zastosowane technologie</h3>

<h4>Google cloud Kubernetes engine</h4>

Otwartoźródłowa platforma do zarządzania, automatyzacji i skalowania aplikacji kontenerowych. Jego pierwotna wersja została stworzona w 2014 roku przez Google, a obecnie rozwijany jest przez Cloud Native Computing Foundation.
Jest to gotowa usługa do wdrożenia obiektów kubernetesa google sam tworzy nam cluster i konfiguruje maszyny VM w google compute na których mozemy implementować naszę aplikacje bez martwienia się maszynami wirtualnymi podspodem clustra.


Google cloud load balanser
Load balanser w google cloud jest bardzo rozbudowany i może działać na róznych warstwach sieciowych 4 lub 7. 
Jest to load balanser softerowy, bardzo łatwo się skaluje jest równiez bardzo wydajny jest wstanie przyjać milion zapytań na sekunde 

<h4>Google cloud persistent volumes</h4>

W przeciwieństwie do Volumes, cyklem życia PersistentVolumes zarządza Kubernetes. PersistentVolumes można dynamicznie udostępniać; użytkownik nie musi ręcznie tworzyć i usuwać kopii zapasowej.
<br>
PersistentVolumes to zasoby klastra, które istnieją niezależnie od zasobników. Oznacza to,że dysk i dane reprezentowane przez PersistentVolume nadal istnieją w miarę zmian w klastrze oraz w trakcie usuwania i ponownego tworzenia zasobników. Zasoby PersistentVolume mogą być udostępniane dynamicznie za pośrednictwem PersistentVolumeClaims lub mogą być jawnie tworzone przez administratora klastra.


<h4>Traefik</h4>
Traefik to open-source Edge Router, jest routerem wartswy 7. Który sprawia że otrzymuje żądania w imieniu systemu i dowiaduje się, które komponenty są odpowiedzialne za ich obsługę.Zapinamy do niego naszą domenę i wskazujemy mu odpowiedni komponent po który ma uderzać na poszczególnej domenie lub sciezce w domenie np edomin.pl/jenkins 


<h4>Docker</h4>
Docker jest otwartym oprogramowaniem służącym do realizacji wirtualizacji na poziomie systemu operacyjnego (tzw. „konteneryzacji”), działające jako „platforma dla programistów i administratorów do tworzenia, wdrażania i uruchamiania aplikacji rozproszonych”.<br>

Docker jest określany jako narzędzie, które pozwala umieścić program oraz jego zależności (biblioteki, pliki konfiguracyjne, lokalne bazy danych itp.) w lekkim, przenośnym, wirtualnym kontenerze, który można uruchomić na prawie każdym serwerze z systemem Linux

<h4>Nginx</h4>
Serwer WWW (HTTP) oraz serwer proxy dla HTTP.<br>
Zaprojektowany z myślą o wysokiej dostępności i silnie obciążonych serwisach (nacisk na skalowalność i niską zajętość zasobów). Wydawany jest na licencji BSD. 



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


<h3>Zastosowanie</h3>

Projekt można zastosować do wdrożenia aplikacji webowej której chcemy zapewnić wysoką dostepność oraz łatwość w skalowaniu.


<h3>Eksploatacji projektu</h3>

Sprawdzam czy Load balancer działa prawidłowo i czy działa round robin. usługa nginx zwraca adres ip poda oraz hostname wiec nadaje sie idealnie do tego aby to sprawdzić

lynx http://edomin.pl/nginx

<h4>Pierwsza próba</h4>

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/ngnix1.png)


<h4>Druga próba</h4>

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/ngnix2.png)



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

Utrzymanie aplikacji dzieki zastosowanie rozwiazanie w chmurze jest duzo prostrze do utrzymania niz jesli chcielibysmy klaster kubernetesa utrzymywać lokalnie 
Odchodzą nam taki problemy jak zarzadzanie samym klastrem  kubernetesa.
Nie interesuje nas rowniez warstwy nizsze takie jak sprzet fizyczny tzn servery, routery, macierze dyskowe oraz backupowanie mastera w klastrze kubernetesa.
Rownież mamy o wiele łatwiejsze skalowanie samej infrastruktóry ponieważ bardzo łatwo mozmey dorzucić kolejne nody do klastra kubernetesa lub zmienjszyć ilość nodów w klastrze.
Wystartowanie nowych nodów w google cloud trwa minitu. A w infrastruktórze własnej mogło by nawet trwać miesiąć jesli mósielibysy zamówićnowy server dostarczyc go do DC oraz wpiąć go do klastra.


<h4>Jak skalować aplikacje</h4>

Obiekty depolyment który zastosowaliśmy możemy ustawić automatyczne skalowanie przez odanie minimalnej oraz maksymalnej liczby podów. <br>
Pody automatycznie się zeskalują do wartości maksymalnej podczas duzego obciazenia podów , oraz gdy obziazenie się zniejszy ilość podów znowu wróci do mniejszej liczby podów



Możemy rownież recznie wymusić skalowanie 

Domyślnie cheese-deployments.yaml zawiera każdego poda w dwóch replikach. Ale możemy łatwo zeskalować wszystkie obiekty zawarte w tym pliku za pomocą 

<b>kubectl scale --replicas=3 -f cheese-deployments.yaml</b>

![Diagram](https://github.com/en696/P3/blob/master/Obrazki/scale.png)


<b>kubectl get pod  --namespace=projekt</b>


![Diagram](https://github.com/en696/P3/blob/master/Obrazki/skalowanie.png)


<h4>Jak wgrać nową wersje aplikacji</h4>


<h4>Jak diagnozować kontener z aplikacją</h4>


<h4>Jak diagnozować problemy z niedziałajaca aplikacja</h4>


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
