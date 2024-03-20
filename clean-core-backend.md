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
