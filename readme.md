# IoT Data Modelling using Neo4j Graph Database
Please check the article from [IoT Data Modeling in Neo4j Graph DB](https://emergingtechs.net/iot-data-modeling-in-neo4j-graph-db/)

The reference IoT Data Model that we are going to implement in Neo4j is as follows:
![IoT Data Model Graph Schema](https://emergingtechs.net/wp-content/uploads/2019/05/IoT-db-schema.png)
Here, we have ten(10) types of **nodes**. They are:
| Node | Description | Values | Properties
| ------ | ------ | ------ |------ | 
| Thing | Thing is the actual IoT Device for which we are creating this data model | 11 Things configured. Thing01..Thing11 | {id,name,lat,lon} |
| Power | Power source for the Thing. | Battery and Solar | {id, name} |
| Thing Type | What type of Thing is it. | Soil Moisture, Water Quality, Light Control (NEMA 7-Pin) |{id, name} |
| Module | Any extra module attached to it? | GPS |{id, name} |
| Vendor | Vendor or Integrator for this Thing (ie in case of support and maintenance) | Vendor-1, Vendor-2 and Vendor-3 |{id, name} |
| Manufacturer | Who manufactured this Thing | Libellium, SenRa |{id, name} |
| Network | Which communication network this Thing is using | LoRaWAN, Sigfox, NB-IoT, GPRS |{id, name} |
| Sensor | A Thing can be composed of multiple sensors | Temperature, Heat, Infra Red |{id, name} |
| Application | These are the application which are using the Thing | Application-01, Dashboard-02, Mobile App-03, Mobile App-04, Application-05, Dashboard-06|{id, name} |
| Department | Departments of the organization which are using/interacting with the Thing through Applications | Information Technology, Field Services, Water Management |{id, name} |

Also we have ten(10) edges (ie. Relationships). They are:

| Edge | Description | Properties |
| -----| -----------| ------------|
|-[IS_POWERED_BY]-> (Power) | Represents how the Thing is powered by. Thing can be powered by Battery or Solar in our case | Battery: {lifestarts,lifefinish,installeddate}; If Solar then {dateinstalled,expectedinspectiondate} |
|[IS_USING]->|Which network the Thing is using. Relation can point to LoRaWAN, SigFox, NB-IoT or GPRS node|{appid,devid} if it is using LoRaWAN|
|[IS_COMPOSED_OF]->	| This Thing is composed of multiple sensors may be. For each sensor there will be a relationship. Like for example,  _(Thing)-[:IS_COMPOSED_OF]->(Heat Sensor)_ and _(Thing)-[:IS_COMPOSED_OF]->(Motion Sensor)_ And property for this relationship will hold the range for that sensor.|{low,high} ie the range|
|All other edges| | No properties defined |

## Steps to create Things Neo4j Database

Neo4j provides a FREE (for 3 days I Think) sandbox to create an play around. 

1. [Create Neo4j Sandbox](https://neo4j.com/sandbox-v2/)
![Create Sandbox](https://emergingtechs.net/wp-content/uploads/2019/05/image-1-create-sandbox.png)
2. Then go to your Neo4j Browser
![Neo4j Browser](https://emergingtechs.net/wp-content/uploads/2019/05/image-2-Neo4j-Browser.png)
3. When the browser opens, you can copy the URL which will be your Neo4j Sandbox URL where you will be able to execute Cypher queries to create the Nodes and Edges and then query to get results
![Neo Query](https://emergingtechs.net/wp-content/uploads/2019/05/neoj-browser-1.png)
4. I already created CSV file for **nodes** and **edges**. You can just copy the Cypher queries and execute in order which will then create our IoT Graph Data Model in Neo4j Sandbox.
5. **Things**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/things.csv" AS csvLine
CREATE (`csvLine.id`:Thing {id: csvLine.id, name: csvLine.name, lat: csvLine.lat, lon: csvLine.lon })
```
6. **Applications**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/applications.csv" AS csvLine
CREATE (`csvLine.id`:Application{id: csvLine.id, name: csvLine.name})
```
7. **Manufacturers**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/manufacturers.csv" AS csvLine
CREATE (`csvLine.id`:Manufacturer {id: csvLine.id, name: csvLine.name})
```
8. **Modules**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/module.csv" AS csvLine
CREATE (`csvLine.id`:Module {id: csvLine.id, name: csvLine.name})
```
9. **Networks**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/network.csv" AS csvLine
CREATE (`csvLine.id`:Network {id: csvLine.id, name: csvLine.name})
```
10. **Power Sources**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/power.csv" AS csvLine
CREATE (`csvLine.id`:Power {id: csvLine.id, name: csvLine.name})
```
11. **Sensors**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/sensors.csv" AS csvLine
CREATE (`csvLine.id`:Sensor {id: csvLine.id, name: csvLine.name})
```
12. **Thing Types**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/thingtype.csv" AS csvLine
CREATE (`csvLine.id`:Thingtype {id: csvLine.id, name: csvLine.name})
```
14. **Vendors**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/vendors.csv" AS csvLine
CREATE (`csvLine.id`:Vendor {id: csvLine.id, name: csvLine.name})
```
16. **Departments**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/departments.csv" AS csvLine
CREATE (`csvLine.id`:Department {id: csvLine.id, name: csvLine.name})
```
17. **Create Edges**
```sh
LOAD CSV WITH HEADERS FROM "https://raw.githubusercontent.com/muntasirjoarder/NeoThings/master/relation.csv" AS rels
match (from {id:rels.thingId}), (to {id: rels.entityid})
CALL apoc.create.relationship(from, rels.relationshipname,apoc.convert.fromJsonMap(rels.prop), to) YIELD rel 
RETURN count(*)
```
18. **See how it looks**
Execute
```sh
MATCH (n) RETURN n
```
If all the steps are completed properly then the Graph should look like:
![IoT Graph DB](https://emergingtechs.net/wp-content/uploads/2019/05/everything-1.png)

Now run the Cypher queries those I specified in the article [IoT Data Modeling in Neo4j Graph DB](https://emergingtechs.net/iot-data-modeling-in-neo4j-graph-db/)
