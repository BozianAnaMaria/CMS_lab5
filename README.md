# Raport: Lucrarea de laborator nr. 5. Securitatea WordPress

## Scopul lucrării

Securizarea unei instalări WordPress clasice prin gestionarea rolurilor, hardening, monitorizare și configurarea pluginurilor All In One WP Security & Firewall (AIOS) și UpdraftPlus pentru backup.

---

## Partea I: Desfășurarea lucrării (Pas cu Pas)

### Pasul 1. Pregătirea mediului de lucru

Am activat modul de depanare adăugând define('WP_DEBUG', true); în fișierul wp-config.php pentru a vizualiza eventualele conflicte de securitat

```php
define('WP_DEBUG', true);
```

Această setare ne asigură vizibilitate maximă în cazul apariției conflictelor de securitate.

![Afișarea editării wp-config.php și adăugarea directivei WP_DEBUG, vizualizată în editorul de cod](images/image%20copy%203.png)


### Pasul 2. Gestionarea rolurilor și politicilor de parole stricte

Am navigat în panoul de administrare la secțiunea **Users (Utilizatori) -> Add New (Adaugă utilizator nou)**. Cu scopul de a izola activitățile cu impact general pe site și a nu le comasa exclusiv sub puterea super-admin-ului, am generat un utilizator adițional limitat, dotat cu rolul de **Autor**. Acest cont este dedicat publicării unice de conținut curat și nu va avea nicio interfață privind adăugarea setărilor nucleare ori instalarea plugin-urilor.  

![Interfața de configurare și de adăugare a noului cont de utilizator cu nivel de Autor](images/image.png)

Confirmarea completă a înregistrării s-a materializat odată ce acest utlizator Autor a fost generat și a apărut prezentat distinct în lista unificată de administrare. 

![Lista de utilizatori, din care reiese clar stabilirea contului separat Autor definit via email distinct](images/image%20copy.png)


### Pasul 3. Mentenanța și actualizările sistemului (Core, Teme și Pluginuri)

Accesând meniul structurat sub elementul **Dashboard -> Updates (Panou de control -> Actualizări)** m-am asigurat, direct din sursele oficiale, că instalarea curentă utilizează în mod activ ultima versiune stabilă a pachetului WordPress Core. Totodată, verificările au asimilitat faptul că orice temă adițională prestabilită (ex. Twenty Twenty-Four, sau teme clasice incluse automat) și eventualele componente plugin erau strict „Up to date”.

![Interfața de actualizări ce atestă confirmarea aplicării la nivel de aplicație a ultimei versiuni stabile de funcționare](images/image%20copy%202.png)


### Pasul 4. Hardening de bază (Restricționarea editării și limitarea accesului web server)

Dezactivarea editorului de teme: Am adăugat define('DISALLOW_FILE_EDIT', true); în wp-config.php pentru a preveni injectarea de cod (RCE) direct din interfața de administrare.

```php
define('DISALLOW_FILE_EDIT', true);
```

Protejarea fișierelor sensibile: Am blocat accesul extern la wp-config.php adăugând următoarele reguli în fișierul .htaccess:

```apache
<files wp-config.php>
   order allow,deny
   deny from all
</files>
```

![Codul definitivelor de restrictionare a accesului în fișierul Apache .htaccess sub blocul de siguranță asigurat pentru modulul fișierelor](images/image%20copy%204.png)


### Pasul 5. Instalarea și configurarea avansată folosind modulul All In One WP Security & Firewall (AIOS)

Peste blocarea manuală primară am considerat critic necesar extinderea măsurilor superioare care necesită monitorizare vizuală complexă bazată pe detecție automată, operare asimilată de instrumente centralizate de audit WAF (Web Application Firewall). Drept urmare, a fost instalată și declarată activă platforma superioară **All In One WP Security & Firewall (AIOS)**. Interfața inițială integrată aduce undă verde ce confirmă procedeele fundamentale ce puseseră stăpânire de acțiune protejând mediul pre-operațional în fundal.

![Panoul finalizat de întâmpinare cu mesaj de succes ulterior primei proceduri de activare a măsurilor obligatorii preinstalate de AIOS (You're all set)](images/image%20copy%205.png)

Pentru o rată solidă de acoperire care să depășească gradul mediocru de atac, am rearanjat și manipulat o listă stufoasă de opțiuni din configurația plugin-ului, conform practicilor următoare:

#### A. Manipularea conturilor de utilizatori vulnerabile (Renamed Admin - module User Accounts)

Am schimbat numele contului implicit din „admin” în admin132.

![Instanța nativă AIOS recomandă imperios și execută modificarea schimbării contului super-admin asimilat din oficiu cu referință simplă](images/image%20copy%208.png)

#### B. Restricția procesului de înregistrare automată (Manual Approval Registration - Spam Control)

Am setat aprobarea manuală a conturilor noi pentru a bloca spammerii

![Sistemul ce dovedește detaliat configurarea manualizării cu rol esențial ca filtru la primirea noilor membrii exclusiv](images/image%20copy%209.png)

#### C. Politici anti-Brute-Force și rate limitare IP (Login Lockdown Module)

Am limitat tentativele de autentificare: blocare IP de 30 de minute după 5 eșecuri într-un interval de 15 minute.

![O previziune referentă direct setărilor aplicate sever ale ferestrei interval de retry scurte ca permisiuni pe timpi de respingere asigurați de lockdown (30m)](images/image%20copy%206.png)

#### D. Extragerea sesiunilor paralele latente expuse prin deconectare automată (Force Logout Control)

Am impus deconectarea automată (Force Logout) a tuturor utilizatorilor la fiecare 24 de ore (1440 minute).

![Restricționarea sesiunilor valabile ale administrării pe prag setat limitativ periodic ce produce Forcing validare via timeout general după durata (1440m)](images/image%20copy%207.png)

#### E. Schimbarea topologiei endpoint-urilor (Mascarada via Rename Login Page Module)

Am redenumit pagina implicită wp-login.php în /login-securizat.

![Setările modulelor Brute force ce certifică redirecționarea interfeței convenționale prin noul element invizibil wp (login-securizat)](images/image%20copy%2014.png)

#### F. Inspecțiile strat-bază POSIX ce acoperă nivelele OS fizice instalate (Filesystem Structure / Permissions)

Am verificat ca folderele să aibă permisiuni 755, iar fișierele 644

![Ecran de identificare sistem și de adaptabilitate ce analiază permisiunile redate specific cu modul Windows ca scanner in modul File Permission Scan](images/image%20copy%2010.png)

#### G. Integrarea ierarhiilor prin Firewall Web App și Politici HTTP speciale


Am limitat upload-ul la 100MB, am blocat interogările HTTP periculoase (XSS/SQLi) și am activat filtrarea avansată a șirurilor de caractere.

![Setările împotriva preluării string-urilor de manipulari de input interzise referitoare la detecția XSS din tab-ul bad query parameter](images/image%20copy%2012.png)

Asigurând elementele mai nuanțate din aceeași stivă, sub **String filtering** a fost impus să se declanșează obligativitatea regulii **"Enable advanced character string filter"**. Extinderea funcționează ca senzor special care anulează, ca o politică aspră specifică, semnătura și ansamblul secvențial a seturilor pe litere asimilitate metodelor complicate de evadări specifice pattern-urilor asimilate la testele malițioase (XSS Strings Patterns inserate des prin manipulare pe mesaje formulare).
![Procedurii interne speciale String Filtering setată ca ariergarda detecțiilor secvențelor de caractere pe atacatori complecși payload XSS](images/image%20copy%2013.png)


#### H. Auditări avansate pentru schimbările coruptibile invizibile direct (Scanner Malware) & Alertele Email 
Platforma nu a omis uneltele automatizate asincrone a căror procedură necesită raportarea integrității prin monitorizare. A fost declanșat prin File security / *Files change detection settings*, procesorul de cron-scanning programat ce evaluează constant starea inițială generală. Măsura compară hashurile a ce fișiere erau anterenior stocate și a fost instigat să ruleze scanul integrat și să acționeze sistematic evaluarea o dată la "4 săptămâni". În acest fel aflăm din timp la coruperea unui fișier sub o atacator masivă fără prezența alterăriilor vizibilă a formei web (Backdoor de server rootkits stealthy).  
![Setarea de rutine Scanner cron control periodice ca proces periodic pe monitorizare la detecții e fișierelor interne editae malițioase invizivile general](images/image%20copy%2015.png)

Aproape final a adus notificarea promptă vizuală – nu era permis lăsarea incidentului de izolare netrasmis a elementului de urgență. Reglementarea **Notifications alert settings (Notify by email)** a obligat serverul la o alertare direct pe in-box-ul cu o sursă pre-asigurată dedicat profilului admnistrator (așa cum am indicat via formular e-mail) a incidentelor care dovedesc cum și cine este atacat exact când cineva a atins Lockout rate max fail attempts pe firewallul de Brute Force per sesiune din dashboard loging limitări proceduri.
![Prezența doveditelor completărilor de trimitere informației IP blocate orientativ a rapoartelor prin email alert module preconfigurat adminilor aditionali](images/image%20copy%2019.png)


### Pasul 6. Sistem Garantat Defensiv folosind Extensii Backup periodice (UpdraftPlus Module Settings)

Am configurat backup-uri automate lunare pentru Baza de Date, cu o retenție de 2 arhive.

![Formularele Settings general administrative din planul setărilor Schedule implementat pentru Database periodice și limitative arhivari de reținere Fișiere Manual pe asistentul de proces Backup Schedule Updraft](images/image%20copy%2018.png)

Am efectuat un backup manual complet și am descărcat arhivele local (offline), pregătite pentru Disaster Recovery.

![Lista existentului tablou de stoc local generalist și arhival reținut din istoricul indexat per pacheții Backup SQL curat preluati integral local](images/image%20copy%2016.png)

Rapoartele finale au ridicat asistența completă prin manevrarea arhivelor logice DB selectate individual cu suport complet descărcabile local pentru extragerea acestora prin funcția *Download to your computer*. Componentă care oferă acuratețe garanției unei rețete offline salvate izolat ca transfer arhive date direct referențiate zip. Gata garantat direct a o prelua la nevoie Disaster recovery spre transfer curățat (Offsite-cloud remote-system host backup), o decizie care blindează structurile a nu a sta centralizate strict pe discul ce funcționează instanța curentă. S-a realizat extragerea arhivei pure g-zip offline.
![Sistemul confirmă ca fișierul bază deține stocare curățată independentă pe descărcare spre stadiul GZ fișiere de suport arhivări separate client terminalului extern offsite procedurii recovery via Updraft modal function extragere Download](images/image%20copy%2017.png)



### Pasul 7. Etapa Supremă de Certificări prin scenarii de validare (Test Blocking Brute-Force limit și Verificarea Datelor Restabile via Restore procedure)

Dovada existenței sistemulilor asigurate necesită certitudini obținute exclusiv testării negative (simularea ca penetration testing din unghiul vizat adversar negativ atacator) pe ambele brațe asimilate ca element de limită activ și validare garanție funcțională la moment post-avarie irrecuperabile date umane gresite accidental ștergere. 

**Exercițiul A. Test de izolare restrictivă prin agresiune IP blocat (Rate-Limiting firewall trigger):**

Am introdus intenționat parole greșite de 6 ori pe pagina /login-securizat. Rezultat: IP-ul meu a fost blocat instantaneu, iar incidentul a fost înregistrat corect în logurile AIOS.

![Vizualizarea efectelor confirmante la excluderea direct din platforma panoului asigurat izolat la interceptie repetari asalt refuz cu limitator text alertă general simplificat](images/image%20copy%2023.png) 

![Meniurile demonstrează centralizatorul rapoartelor procedurale la închiderea agresiunilor stivuind tabele referențiale interne și dovedind eficienta izolare alocat pe tablou activ lockouts per surse adrese](images/image%20copy%2024.png)


**Exercițiul B. Procedură extremă de recuperare și readucerea consistenței stării (Avarii provocate intern / Ștergere a articolelor referințele Post Content Destroy Test via Undo Backup Recovery):**  

Am șters intenționat un articol (inclusiv din Trash). Rezultat: Am folosit arhiva de backup din UpdraftPlus pentru a restaura baza de date, iar articolul a reapărut intact pe site.

![Funcția prin ce s-a executat trimitere conținut activ articol direct spre coș prin acționarea manual butoanelor element web panel articol edit selecții post editor pe confirmă ștergere intenționată referenția (Trash item event call)](images/image%20copy%2020.png)
 
![Vizita interfeței curentei confirmând redus panoul la indexare ce postările nu recunosc element articol pre-stabilit anterior din stadii active publice dispărut clar via listă golită la bază totală reținut doar zona sub trashed post (Gunoaie depozite)](images/image%20copy%2021.png)

![Rularea ferestrei meniu cu asistent avansat asimilând preparativ local arhive și vizual pregătirea fișiere a log core pentru refaceri procedurii finale via proces decizional restaurativ manual decurs sistem preparativ la cerere Restore Confirmations DB panel popup confirmare acțiuni interfață curată izolat local Updraft component Restore preluari arhive DB backup fișier](images/image%20copy%2022.png)

Fără probleme aplicația UpdraftPlus sub instigare rulându-se a finalizat corelat preluarea pe pchetele arhivă proces subloc structură DB tabelară din backup preștergere rescriind direct complet. 

![Confirmarea rapoartelor interfață asimilate din terminal alert de refaceri pe operațiunea integral curatasucces final ce nu relatează incidente pe acoperi refacerea pe proces complet "Restore Succesful" pe meniu execuției dezarhivare component arhive SQL logs din Updraft Restoration Console logs tab](images/image%20copy%2025.png)

Validăm vizual! Executand o procedură reîmprospătare generală, prelucrând întoarcerea de proces, si revenind navigativ direct sub interfață panoul la zona gestionată - **Posts (Articole publice) panou**. Reușită!

![Panoul cu Postări ce ne aduce confirmări că referință la structura elementului complet revine din instanța prealabil distrus refăcut via baze integrând element re-curent live articol refăcut prin arhive și restaurare general succes preluari date live index](images/image%20copy%2026.png)


---

## Partea a II-a: Răspunsuri la întrebările de control

**1. De ce DISALLOW_FILE_EDIT și permisiunile corecte pe wp-config.php reduc semnificativ riscul post-exploit?**  

DISALLOW_FILE_EDIT previne atacatorii care au compromis un cont de admin să injecteze cod PHP malițios (backdoors/shells) direct din editorul vizual WordPress.

Securizarea wp-config.php (prin permisiuni de citire restrânse și .htaccess) previne furtul credențialelor bazei de date și a cheilor criptografice (salts), blocând compromiterea totală a infrastructurii.

**2. Ce setări ai ales pentru Login Lockdown/Firewall și de ce (explică echilibrul între securitate și experiența utilizatorului)?**  

Am ales blocarea IP-ului timp de 30 de minute după 5 încercări eșuate în 15 minute. Această configurație oprește eficient atacurile automate de tip brute-force lansate de boți, dar este suficient de permisivă pentru a nu bloca permanent un utilizator legitim care doar și-a tastat greșit parola de câteva ori.

**3. Cu ce se deosebesc măsurile de protecție la nivel WordPress (plugin/WAF) față de cele la nivelul serverului web și al sistemului de operare?**  

Nivel WordPress (WAF/Layer 7): Acționează la nivelul aplicației. Înțelege contextul (utilizatori, permisiuni, payload-uri SQLi/XSS) și poate bloca comportamente malițioase specifice (ex. comentarii spam, url-uri ascunse).

Nivel Server/OS (Layer 3/4): Acționează brut, la nivel de rețea (ex. IPtables, limitări porturi TCP). Nu înțelege logica WordPress, dar este excelent pentru a bloca pachete malițioase înainte să consume resursele serverului (ex. mitigarea atacurilor DDoS).

**4. Ce trebuie inclus neapărat într-un backup „complet” WordPress și cum verifici dacă restaurarea funcționează cu adevărat?**  

Un backup complet necesită două componente obligatorii: Baza de date (structura SQL, postări, setări) și Fișierele aplicației (folderele wp-content/, teme, pluginuri, fișiere media din uploads/, și fișierele de configurare precum wp-config.php).

Verificarea nu se face doar uitându-te la dimensiunea arhivei. Confirmarea sigură se face doar printr-un test de restaurare (Dry Run/Staging): instalarea arhivei pe un mediu de test izolat și verificarea manuală a funcționalității (linkuri valide, imagini afișate corect, lipsa erorilor 404).
