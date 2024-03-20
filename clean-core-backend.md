## Schema.cds
```js
namespace p2c.cap.contracts;
using { cuid , managed } from '@sap/cds/common';

entity Contracts : managed {
	key ID : String;
	description : String;
	beginDate : Date;
	endDate : Date;
	totalMonthValue : Integer;
	totalMonthCurrency : String;
	//businessPartner: Association to BusinessPartners;
	contractItems : Composition of many ContractItems on contractItems.contract = $self;
	bpName:String;
}

@cds.autoexpose
	entity ContractItems : cuid {
	key contract: Association to one Contracts;
	beginDate: Date;
	endDate: Date;
	price: Integer;
	priceCurrency: String;
	tool: String;
	toolName: String
}
```


## admin-service.cds
```js
using { p2c.cap.contracts as my } from '../db/schema';

service AdminService @(path: '/admin'){
	entity Contracts as projection on my.Contracts;
	entity ContractItems as projection on my.ContractItems;
}
```

## contracts-service.cds

```js
using { p2c.cap.contracts as my} from '../db/schema';

service  ContractsService @(path:'/contracts'){

    entity Contracts as select from my.Contracts {
        ID, description
    };

    @readonly entity ContractItems as projection on my.ContractItems;


}
```

## db/data/p2c.cap.contracts-Contracts.csv

```csv
ID,description,beginDate,endDate,totalMonthValue,totalMonthCurrency
CN-100,CCL Mining CO - Perforation 2023,2023-03-01,2023-10-25,0,USD
CN-105,Pacific Transportation INC. South Africa Mining,2022-02-02,2028-04-02,0,USD
CN-106,Atlantic Transportation INC. - Cooper Mine-Port Movement,2023-02-02,2026-05-18,0,USD
CN-108,Pure Gold Company - Explosives and Perforation, 2023-02-02,2025-12-02,0,USD
CN-109,Rusell & CO Diamond Mining - Australian Full Operation Contract,2023-03-03,2030-10-18,0,USD

```

## db/data/p2c.cap.contracts-ContractItems.csv

```csv
ID,contract_ID,beginDate,endDate,price,priceCurrency,tool,toolName

02d29d9d-3538-4140-8381-67ee542928db,CN-100,2023-03-01,2023-03-30,5700,USD,65d608bafc04cd3d303714a1,EQ-302-DR-01
0b9e270f-0d11-4dd1-bb52-976812bcd32f,CN-100,2023-03-01,2023-03-10,180000,USD,65d608bafc04cd3d30371375,TR-101-HT-01
63f57654-ee43-4f05-8fa2-7fd3e240517f,CN-105,2022-02-02,2022-02-02,190,USD,65d608bafc04cd3d303714a3,EQ-302-DR-03
a324e459-a773-4616-8f48-dc60a3ab890f,CN-106,2023-02-02,2023-02-02,26019,USD,65d608bafc04cd3d30371388,TR-101-HT-20
ac184d7a-b485-493e-be77-9d34c2d76407,CN-108,2023-02-02,2023-02-02,17000,USD,65d608bafc04cd3d30371377,TR-101-HT-03
cef9a93e-2f97-47ec-8256-7ae3ef7b7704,CN-109,2023-03-03,2023-03-03,17000,USD,65d608bafc04cd3d30371378,TR-101-HT-04

```

## external-test.cds

```js
using { p2c.cap.contracts as my } from '../db/schema';


service DemoService @(path: '/demosrv'){

    //**Business Partner integration from S4HANA */

    @readonly
    // entity BusinessPartners as projection on my.BusinessPartners;
    entity Contracts as projection on my.Contracts;
    entity ContractItems as projection on my.ContractItems;
}
```


## srv/external/data/API_BUSINESS_PARTNER-A_BusinessPartner.csv

```csv
BusinessPartner;BusinessPartnerFullName;BusinessPartnerIsBlocked
1000038;Williams Electric Drives;false
1000040;Smith Batteries Ltd;false
1000042;Johnson Automotive Supplies;true

```

## Add the business partner entity to schema.cds

```js
using { API_BUSINESS_PARTNER as bpar } from '../srv/external/API_BUSINESS_PARTNER.csn';

entity BusinessPartners as projection on bpar.A_BusinessPartner {
    key BusinessPartner,
    BusinessPartnerFullName,
    BusinessPartnerIsBlocked 
}
```

# Uncomment line on external-test.cds!!!
