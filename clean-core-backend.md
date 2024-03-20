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

