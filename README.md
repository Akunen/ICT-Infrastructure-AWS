## ICT-Infrastuktuuri pilvialustalla -kurssin päättötyö

Tätä päättötyötä varten halusin tehdä arkkitehtuurin Minecraft-serverille, joka tulisi itselleni 
käyttöön, ja jota voisin jatkokehittää tulevaisuudessa. Arkkitehtuurin laitoin pystyyn
Cloudformation templaattia käyttäen.

Templaatti koostuu seuraavista komponenteista:

VPC (Virtual Private Cloud): Virtuaalinen verkkoympäristö, jolla voidaan eristää 
infrastruktuuri. Mahdollistaa aliverkkojen määrittelyn sekä lisää turvallisuutta ja korkeaa 
käytettävyyttä. 

InternetGateway: Tämä liitetään VPC:hen. Mahdollistaa liikenteen internetin ja VPC:n välillä.
RouteTable: Määrittelee miten liikenne ohjataan aliverkoissa ja VPC:ssä.
Route: Tässä templaatissa määrittää reitin 0.0.0.0/0-osoitteeseen, mikä tarkoittaa kaikkea 
liikennettä. 

SubnetA ja SubnetB aliverkot: Nämä ovat aliverkot, jotka sijaitsevat eri saatavuusalueilla. Ne 
lisäävät korkeaa käytettävyyttä, jos esimerkiksi toisessa saatavuusalueessa on ongelmia. Ne 
myös jakavat kuormaa keskenään. 

SubnetARouteTableAssociation ja SubnetBRouteTableAssociation: Liittävät aliverkot 
reititystauluun, jotta liikenne ohjataan oikein. Tämä varmistaa, että liikenne kulkee oikeiden 
aliverkkojen läpi.

Ec2SecurityGroup: Palomuurisäännöt, jotka sallivat tai estävät liikenteen instansseille. Tässä 
tapauksessa SSH-liikenne sallitaan vain tietystä IP-osoitteesta ja Minecraft-liikenne (Portti: 
25565) sallitaan mistä tahansa IP-osoitteesta.

LoadBalancerSecurityGroup: Palomuurisäännöt Load Balancerin käyttämälle portille 25565. 
Tämä mahdollistaa Minecraft-liikenteen kulun Load Balancerin kautta.
2

NetworkLoadBalancer: kuormanjakaja, joka vastaanottaa serverillä pelaavien yhteydet ja 
jakaa ne tasaisesti instansseille saatavuusalueiden välillä. Tämä parantaa suorituskykyä ja 
käytettävyyttä. Käytin Network Load Balanceria, koska se tukee TCP ja UDP protokollia, joita 
Minecraft -serveri käyttää.

TargetGroup: Ryhmä instansseja, joihin Network Load Balancer ohjaa liikenteen.
LoadBalancerListener: Kuuntelija, joka vastaanottaa yhteydet Load Balancerille ja ohjaa ne
Target Groupiin.

LaunchConfiguration: Käynnistyskonfiguraatio, joka määrittää instanssien asetukset, kuten 
käyttöjärjestelmän, koon ja käyttöoikeudet.

AutoScalingGroup: Automaattisen skaalauksen ryhmä, tässä tapauksessa se varmistaa, että 
aina on vähintään kaksi instanssia käytettävissä. Esimerkiksi jos yksi instanssi kaatuu, tämä luo 
uuden instanssin tilalle. 

InstanceProfile: IAM-profiili instansseille.

InstanceRole: IAM-rooli, joka määrittää instanssien oikeudet, kuten pääsyn EC2-instanssin 
tietoihin ja ominaisuuksiin.


