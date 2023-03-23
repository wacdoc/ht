# Bati pwòp sèvè SMTP voye lapòs ou

## preambul

SMTP ka achte dirèkteman sèvis nan men vandè nwaj yo, tankou:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali nwaj imel pouse](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Ou kapab tou bati pwòp sèvè lapòs ou - voye san limit, pri jeneral ki ba.

Anba a, nou montre etap pa etap ki jan yo bati pwòp sèvè lapòs nou an.

## Seleksyon sèvè

Sèvè SMTP pwòp tèt ou akomode mande pou yon IP piblik ak pò 25, 456, ak 587 louvri.

Nwaj piblik yo itilize souvan yo bloke pò sa yo pa default, epi li ka posib yo louvri yo lè yo bay yon lòd travay, men li trè anbarasman apre tout.

Mwen rekòmande pou achte nan men yon lame ki gen pò sa yo louvri ak sipòte mete kanpe non domèn ranvèse.

Isit la, mwen rekòmande [Contabo](https://contabo.com) .

Contabo se yon founisè hosting ki baze nan Minik, Almay, ki te fonde an 2003 ak pri trè konpetitif.

Si w chwazi Euro kòm lajan ou achte, pri a pral pi bon mache (yon sèvè ki gen memwa 8GB ak 4 CPU koute apeprè 529 Yuan pou chak ane, ak frè enstalasyon inisyal la gratis pou yon ane).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Lè w ap mete yon lòd, remak `prefer AMD` , ak sèvè a ak CPU AMD ap gen pi bon pèfòmans.

Nan sa ki annapre yo, mwen pral pran VPS Contabo a kòm yon egzanp pou montre kijan pou konstwi pwòp sèvè lapòs ou.

## Konfigirasyon sistèm Ubuntu

Sistèm operasyon isit la se Ubuntu 22.04

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Si sèvè a sou ssh montre `Welcome to TinyCore 13!` (jan yo montre nan figi ki anba a), sa vle di ke sistèm nan poko enstale. Tanpri dekonekte ssh epi tann kèk minit pou w konekte ankò.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

Lè `Welcome to Ubuntu 22.04.1 LTS` parèt, inisyalizasyon an fini, epi ou ka kontinye ak etap sa yo.

### [Si ou vle] Inisyalize anviwònman devlopman an

Etap sa a se opsyonèl.

Pou konvenyans, mwen mete enstalasyon an ak konfigirasyon sistèm nan lojisyèl ubuntu nan [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) .

Kouri lòd sa a pou enstale ak yon sèl klike sou.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Itilizatè Chinwa yo, tanpri itilize kòmandman sa a pito, epi lang, zòn tan, elatriye pral otomatikman mete.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo pèmèt IPV6

Pèmèt IPV6 pou SMTP kapab tou voye imèl ak adrès IPV6.

edite `/etc/sysctl.conf`

Modifye oswa ajoute liy sa yo

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

Swiv leson [patikilye a contabo: Ajoute koneksyon IPv6 sou sèvè ou a](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

Edite `/etc/netplan/01-netcfg.yaml` , ajoute kèk liy jan yo montre nan figi ki anba a (fichye konfigirasyon default Contabo VPS deja gen liy sa yo, jis retire yo).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Lè sa a, `netplan apply` pou fè konfigirasyon an modifye pran efè.

Apre konfigirasyon an reyisi, ou ka itilize `curl 6.ipw.cn` pou wè adrès ipv6 rezo ekstèn ou a.

## Klone operasyon depo konfigirasyon yo

```
git clone https://github.com/wactax/ops.soft.git
```

## Jenere yon sètifika SSL gratis pou non domèn ou

Voye lapòs mande pou yon sètifika SSL pou chifreman ak siyen.

Nou itilize [acme.sh](https://github.com/acmesh-official/acme.sh) pou jenere sètifika.

acme.sh se yon sous louvri zouti siyen sètifika otomatik,

Antre nan depo konfigirasyon ops.soft, kouri `./ssl.sh` , epi yo pral kreye yon katab `conf` nan **anyè anwo a** .

Jwenn founisè dns ou a soti nan [acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) , edite `conf/conf.sh` .

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Lè sa a, kouri `./ssl.sh 123.com` pou jenere sètifika `123.com` ak `*.123.com` pou non domèn ou.

Premye kouri a pral otomatikman enstale [acme.sh](https://github.com/acmesh-official/acme.sh) epi ajoute yon travay pwograme pou renouvèlman otomatik. Ou ka wè `crontab -l` , gen yon liy tankou sa a.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Chemen pou sètifika pwodwi a se yon bagay tankou `/mnt/www/.acme.sh/123.com_ecc。`

Renouvèlman sètifika pral rele script `conf/reload/123.com.sh` , edite script sa a, ou ka ajoute kòmandman tankou `nginx -s reload` pou rafrechi kachèt sètifika aplikasyon ki gen rapò.

## Bati sèvè SMTP ak chasquid

[chasquid](https://github.com/albertito/chasquid) se yon sèvè SMTP sous louvri ki ekri nan lang Go.

Kòm yon ranplasan pou ansyen pwogram sèvè lapòs yo tankou Postfix ak Sendmail, chasquid se pi senp ak pi fasil pou itilize, epi li pi fasil tou pou devlopman segondè.

Kouri `./chasquid/init.sh 123.com` pral enstale otomatikman ak yon sèl klike sou (ranplase 123.com ak non domèn voye ou).

## Konfigirasyon Imèl Siyati DKIM

Yo itilize DKIM pou voye siyati imel pou anpeche lèt yo trete kòm spam.

Apre lòd la kouri avèk siksè, yo pral mande w pou mete dosye DKIM la (jan yo montre anba a).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Jis ajoute yon dosye TXT nan dns ou a (jan yo montre anba a).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Gade estati sèvis ak mòso bwa

 `systemctl status chasquid` Gade estati sèvis la.

Eta a nan operasyon nòmal se jan yo montre nan figi ki anba a

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` oswa `journalctl -xeu chasquid` ka wè jounal erè a.

## Ranvèse konfigirasyon non domèn

Non domèn ranvèse se pou pèmèt adrès IP la rezoud nan non domèn ki koresponn lan.

Mete yon non domèn ranvèse ka anpeche imèl yo idantifye kòm Spam.

Lè yo resevwa lapòs la, sèvè k ap resevwa a pral fè analiz non domèn ranvèse sou adrès IP sèvè ki voye a pou konfime si sèvè ki voye a gen yon non domèn valab.

Si sèvè ki voye a pa gen yon non domèn ranvèse oswa si non domèn ranvèse pa matche ak adrès IP sèvè ki voye a, sèvè k ap resevwa a ka rekonèt imel la kòm Spam oswa rejte li.

Vizite [https://my.contabo.com/rdns](https://my.contabo.com/rdns) epi konfigirasyon jan yo montre anba a

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Apre mete non an domèn ranvèse, sonje konfigirasyon rezolisyon an pi devan nan non an domèn ipv4 ak ipv6 nan sèvè a.

## Edit non host chasquid.conf

Modifye `conf/chasquid/chasquid.conf` pou valè non domèn ranvèse.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Lè sa a, kouri `systemctl restart chasquid` pou rekòmanse sèvis la.

## Sovgad konf nan repozitwa git

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Pou egzanp, mwen fè bak folder nan konf nan pwòp pwosesis github mwen an jan sa a

Kreye yon depo prive an premye

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Antre nan anyè a konf epi soumèt nan depo a

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Ajoute moun k ap voye a

kouri

```
chasquid-util user-add i@wac.tax
```

Ka ajoute moun k ap voye

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Verifye ke modpas la fikse kòrèkteman

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Apre ou fin ajoute itilizatè a, `chasquid/domains/wac.tax/users` yo pral mete ajou, sonje soumèt li nan depo a.

## DNS ajoute dosye SPF

SPF (Sender Policy Framework) se yon teknoloji verifikasyon imel yo itilize pou anpeche fwod imel.

Li verifye idantite yon moun k ap voye lapòs lè li tcheke adrès IP moun k la matche ak dosye DNS non domèn li di li ye, sa ki anpeche èskro yo voye fo imèl.

Ajoute dosye SPF ka anpeche imèl yo idantifye kòm Spam otank posib.

Si sèvè non domèn ou a pa sipòte kalite SPF, jis ajoute dosye kalite TXT.

Pou egzanp, SPF nan `wac.tax` se jan sa a

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

SPF pou `_spf.wac.tax`

`v=spf1 a:smtp.wac.tax ~all`

Remake byen ke mwen genyen `include:_spf.google.com` isit la, sa a se paske mwen pral konfigirasyon `i@wac.tax` kòm adrès ki voye nan bwat lèt Google la pita.

## DNS konfigirasyon DMARC

DMARC la vle di (Domain-based Message Authentication, Reporting & Conformance).

Yo itilize li pou kaptire rebondisman SPF (petèt ki te koze pa erè konfigirasyon, oswa yon lòt moun ap pran pòz ou voye spam).

Ajoute dosye TXT `_dmarc` ,

Kontni an se jan sa a

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Siyifikasyon chak paramèt se jan sa a

### p (Politik)

Endike kijan pou jere imèl ki echwe verifikasyon SPF (Sender Policy Framework) oswa DKIM (DomainKeys Identified Mail). Paramèt p a ka mete nan youn nan twa valè:

* okenn: Yo pa pran okenn aksyon, se sèlman rezilta verifikasyon an bay moun k ap voye a atravè mekanis rapò imel la.
* Karantèn: Mete lapòs ki pa te pase verifikasyon an nan katab spam la, men li pa pral rejte lapòs la dirèkteman.
* rejte: Rejte dirèkteman imèl ki echwe verifikasyon.

### fo (Opsyon pou echèk)

Espesifye kantite enfòmasyon ki retounen nan mekanis rapò a. Li ka mete nan youn nan valè sa yo:

* 0: Rapòte rezilta validation pou tout mesaj yo
* 1: Sèlman rapòte mesaj ki echwe verifikasyon
* d: Sèlman rapòte echèk verifikasyon non domèn
* s: sèlman rapòte echèk verifikasyon SPF
* l: Sèlman rapòte echèk verifikasyon DKIM

### rua & ruf

* rua (Rapò URI pou rapò total): Adrès imèl pou resevwa rapò total
* ruf (Rapò URI pou rapò legal): adrès imel pou resevwa rapò detaye

## Ajoute dosye MX pou voye imèl bay Google Mail

Paske mwen pa t 'kapab jwenn yon bwat lèt antrepriz gratis ki sipòte adrès inivèsèl (Catch-All, ka resevwa nenpòt imèl voye nan non domèn sa a, san restriksyon sou prefiks), mwen te itilize chasquid voye tout imèl nan bwat lèt Gmail mwen an.

**Si ou gen pwòp bwat lèt biznis ou peye, tanpri pa modifye MX la epi sote etap sa a.**

Edit `conf/chasquid/domains/wac.tax/aliases` , mete bwat lèt voye

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` endike tout imèl, `i` se prefiks adrès imel itilizatè ki voye a ki te kreye pi wo a. Pou voye lapòs, chak itilizatè bezwen ajoute yon liy.

Lè sa a, ajoute dosye MX la (mwen lonje dwèt dirèkteman nan adrès non domèn ranvèse isit la, jan yo montre nan premye liy nan figi ki anba a).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Apre konfigirasyon an fini, ou ka sèvi ak lòt adrès imel pou voye imèl bay `i@wac.tax` ak `any123@wac.tax` pou wè si ou ka resevwa imèl nan Gmail.

Si ou pa, tcheke jounal chasquid la ( `grep chasquid /var/log/syslog` ).

## Voye yon imèl bay i@wac.tax ak Google Mail

Apre Google Mail te resevwa lapòs la, mwen natirèlman te espere reponn ak `i@wac.tax` olye de i.wac.tax@gmail.com.

Vizite [https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) epi klike sou "Ajoute yon lòt adrès imel".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Lè sa a, antre kòd verifikasyon ki te resevwa pa imel ki te voye a.

Finalman, li ka mete kòm adrès moun k ap voye default (ansanm ak opsyon pou reponn ak menm adrès la).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Nan fason sa a, nou te konplete etablisman an nan sèvè a lapòs SMTP epi an menm tan an itilize Google Mail pou voye ak resevwa imèl.

## Voye yon imèl tès pou tcheke si konfigirasyon an reyisi

Antre nan `ops/chasquid`

Kouri `direnv allow` yo enstale depandans (direnv te enstale nan pwosesis inisyalizasyon yon sèl-kle anvan an epi yo te ajoute yon zen nan koki a)

Lè sa a, kouri

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Siyifikasyon paramèt yo se jan sa a

* itilizatè: non itilizatè SMTP
* pase: modpas SMTP
* to: destinataire

Ou ka voye yon imèl tès.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Li rekòmande pou itilize Gmail pou resevwa imèl tès pou tcheke si konfigirasyon yo reyisi.

### TLS estanda chifreman

Jan yo montre nan figi ki anba a, gen ti seri sa a, ki vle di ke sètifika SSL la te aktive avèk siksè.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Lè sa a, klike sou "Montre orijinal imel"

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Jan yo montre nan figi ki anba a, paj lapòs Gmail orijinal la montre DKIM, ki vle di konfigirasyon DKIM la reyisi.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Tcheke Receive nan header nan imèl orijinal la, epi ou ka wè ke adrès moun k ap voye a se IPV6, ki vle di ke IPV6 se tou configuré avèk siksè.
