```JSON
{
  "pono": "4500147918",
  "poitem": "00010",
  "docdate": "2026-02-06T12:56",
  "postingdate": "2026-02-06T12:56",
  "personint": "",
  "personext": "",
  "location": "",
  "packageno": "0000000001",
  "shortext": "1234",
  "headertxtlong": "",
  "refdocno": "POSTONPAYSTATION",
  "accasscat": "N",
  "glacc": "5908000100",
  "network": "C.6053.00.011.0.05.12",
  "extnumber": "",
  "testrun": "",
  "option": "2",
  "entrysheets": [
    {
      "packageno": "0000000001",
      "lineno": "0000000001",
      "outlind": "X",
      "subpckgno": "0000000002",
      "qty": "1.00",
      "unit": "EA"
    },
    {
      "packageno": "0000000002",
      "lineno": "0000000002",
      "extline": "0000000020",
      "unit": "EA",
      "shorttext": "50% Design Coordination (2/13)",
      "planpackageno": "0000002748",
      "planpackageline": "0000000131",
      "qty": "388500.00",
      "grprice": "1.00"
    }
  ]
}
```

NO  PMCM
```JSON
{
   "pono":"4100001135",
   "poitem":"00010",
   "docdate":"2026-02-06T12:49",
   "postingdate":"2026-02-06T12:49",
   "personint":"",
   "personext":"",
   "location":"",
   "packageno":"0000000001",
   "shortext":"LLOPL",
   "headertxtlong":"",
   "refdocno":"POSTONPAYSTATION",
   "accasscat":"N",
   "glacc":"5908000100",
   "network":"5001697",
   "extnumber":"",
   "testrun":"",
   "option":"2",
   "entrysheets":[
      {
         "packageno":"0000000001",
         "lineno":"0000000001",
         "outlind":"X",
         "subpckgno":"0000000002",
         "qty":"1.00",
         "unit":"EA"
      },
      {
         "packageno":"0000000002",
         "lineno":"0000000002",
         "extline":"0000000020",
         "unit":"EA",
         "shorttext":"งวดที่ 11 - Dec - 2024",
         "planpackageno":"0000069911",
         "planpackageline":"0000000014",
         "qty":"1027137.55",
         "grprice":"1.00"
      }
   ]
}
```

CSV,

project,title,
C.6031.X.X.X, Medi pataya
C.6032.X.X.X, The Rist

upload -> tempfile
{temfile}
api/v1/projects/validate-import/

resposne --> 
{
	projects: [C.6031.X.X.X, C.6032.X.X.X]
}

api/v1/projects/improt/
{
	projects:[C.XXXX.XX.XX.XX]
}


"compcode eq '6045' and (doctype eq '41' or doctype eq '42' or doctype eq 'Z1') and docdate ge datetime'2000-01-01T00:00:00'"


Choice PO 
```json
{
  "purchase_order": [
    {
      "value": 243,
      "label": "PO-4100002792 | PMCM | บมจ.แอสเสท เวิรด์ คอร์ป",
      "pde_category": "pmcm",
      "currency": "THB",
      "doc_type": "",
      "vendor":{
	      "value": 1,
	      "label": "บจก.มัะมะมะม"
      }
      "exchange_rate": 1,
      "po_number": "4100002792"
    },
    {
      "value": 242,
      "label": "PO-4100000928 | PMCM | บมจ.แอสเสท เวิรด์ คอร์ป",
      "pde_category": "pmcm",
      "currency": "THB",
      "doc_type": "",
      "exchange_rate": 1,
      "po_number": "4100000928"
    },
    {
      "value": 237,
      "label": "PO-4100002708 | Consultant | บจก.ดับเบิ้ลยู เอ แอล คอนซัลแตนท์",
      "pde_category": "consultant",
      "currency": "THB",
      "doc_type": "",
      "exchange_rate": 1,
      "po_number": "4100002708"
    },
    ]
```

PO 1 SAP -- admin edit ได้  
- Bugget : 10,000,000

PO 1 Pay
- Bugget : 10,000,000


RVO 1 
- Open : amount : 10,000,000
-- Apprvoe

RVO 2  --> buget 0  
- Open: amount :  0 
-- Apprvoe 

RVO +- จำนวนเงิน PO 
VO [RVO, RVO] --- update --> PO amout สำหรับทำ PaymentClaim 



---
PO 1 
Buget 20M

RVO 10-1-1
-  ลด 1 
-  ลด 1
- 