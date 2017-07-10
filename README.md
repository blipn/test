# Sofacto-Pay

Link Sofacto wih payment services :

- GoCardLess
- Stripe

# Installation 

Sofacto-Pay (Own Serv)
-

- install & start

```shell
npm install
npm start
```

- Rename config.js.exemple to config.js

Sofacto-Pay (Heroku)
-


**SET your config variables in Heroku / Settings / Config Variables :**

*dont set any port for heroku
    
``` 
# ON HEROKU
DB_USER : user1 
DB_PASS : pass1
ENCRYPTIONKEY : key1 
MASTERPASSWD : key2
MONGODB_URI should be set already
```

push to Heroku (you need heroku cli) :

```shell
git push heroku master
```

MongoDB (Own Serv)
-

- Start MongoDB without access control
```shell
mongod --port 27017 --dbpath /data/db
```
- Connect to the instence
```shell
mongo --port 27017
```
- Create the admin user
```shell
use admin
db.createUser(
  {
    user: "myUserAdmin",
    pwd: "Password",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```
- Re-start the MongoDB instance with access control
```shell
mongod --auth --port 27017 --dbpath /data/db
```
- Authenticate as the user administrator
```shell
mongo --port 27017 -u "myUserAdmin" -p "Password" \
  --authenticationDatabase "admin"
```

MongoDB (Heroku)
-

Install `mLab MongoDB` from Heroku dashboard

#GoCardLess Steps :

- create a Creditor (you)
- create a Creditor Bank Account
- create a Customer
- create a Customer Bank Account
- create a Mandate
- the Customer sign the mandate
- create a Payment

#Stripe Steps :
...

#GoCardLess Routes :

###ALL :

>For all the routes you need theses keys in your header :
```
Content-Type : application/json
x-gcl-token : token privé gocardless
```

>On error usually return :
```json
{ "response":false, "error": "anError" }
```
###Basic :

- Is the server up ? (browser)

    **GET `/`**
```
We're Up
```

- Is the server up ? (json)

    **GET `/up`**
```json
{"up": true}
```
- Webhook url for GCL

    **POST `/gcl/webhooks`**

###Customers :

- Create a new Customer
    
    **POST `/gcl/customer`**

```json
{
     "customers": {
         "email": "jules@maboite.com",
         "given_name": "Jules",
         "family_name": "Ferry",
         "address_line1": "27 route des Alpes",
         "address_line2": "Le Pigoulin",
         "city": "Aix-en-Provence",
         "postal_code": "13100",
         "country_code": "FR",
         "metadata": {
            "salesforce_id": "ABCD1234"
         }
     }
}
```
Return :
```json
{
    "response": {
        "customers": {
            "id": "CU00020ARGQWTC",
            "created_at": "2017-06-27T13:16:43.310Z",
            "email": "jules@maboite.com",
            "given_name": "Jules",
            "family_name": "Ferry",
            "company_name": null,
            "address_line1": "27 route des Alpes",
            "address_line2": "Le Pigoulin",
            "address_line3": null,
            "city": "Aix-en-Provence",
            "region": null,
            "postal_code": "13100",
            "country_code": "FR",
            "language": "fr",
            "swedish_identity_number": null,
            "metadata": {
              "salesforce_id": "ABCD1234"
            }
        }
    }
}
```

- Get a Customer data from ID

    **GET : `/gcl/customer/CU0001Z5SMZYYZ`**
    
Return :
```json
{
    "response": {
        "customers": {
            "id": "CU0001Z5SMZYYZ",
            "created_at": "2017-06-14T15:17:35.594Z",
            "email": "user@example.com",
            "given_name": "Frank",
            "family_name": "Osborne",
            "company_name": null,
            "address_line1": "27 Acer Road",
            "address_line2": "Apt 2",
            "address_line3": null,
            "city": "London",
            "region": null,
            "postal_code": "E8 3GX",
            "country_code": "GB",
            "language": "en",
            "swedish_identity_number": null,
            "metadata": {
              "salesforce_id": "ABCD1234"
            }
        }
    }
}
```

- Create a Customer bank account

    **POST : `/gcl/customer/bank`**
```json
{
     "customer_bank_accounts": {
         "currency": "EUR",
         "iban": "FR14 2004 1010 0505 0001 3M02 606",
         "account_holder_name": "Jules Ferry",
         "country_code": "FR",
         "links": {
             "customer": "CU0001Z5SMZYYZ"
         }
     }
 }
```
Return :
```json
{
  "response": {
    "customer_bank_accounts": {
      "id": "BA0001T2D6YXH4",
      "created_at": "2017-06-27T13:39:33.771Z",
      "account_number_ending": "06",
      "account_holder_name": "JULES FERRY",
      "bank_name": "LA BANQUE POSTALE",
      "currency": "EUR",
      "country_code": "FR",
      "metadata": {},
      "enabled": true,
      "links": {
        "customer": "CU00020ARGQWTC"
      }
    }
  }
}
```

###Creditors :
- Create a new Creditor
    
    _*Soumis à conditions, voir GCL_
    
    **POST : `/gcl/creditor`**
    
```json
{
     "creditors": {
         "name": "Sofacto",
         "address_line1": "Allée de machin",
         "city": "Aix-en-Provence",
         "postal_code": "13090",
         "country_code": "FR"
     }
}
```

- Return a list of all Creditors
    
    **GET : `/gcl/creditors`**
    
Return :
    
```json
{
  "response": {
    "creditors": [
      {
        "id": "CR00004REW8ZF1",
        "created_at": "2017-06-14T13:04:15.020Z",
        "name": "Sofacto",
        "address_line1": null,
        "address_line2": null,
        "address_line3": null,
        "city": null,
        "region": null,
        "postal_code": null,
        "country_code": "FR",
        "logo_url": null,
        "scheme_identifiers": [
          {
            "name": "GoCardless",
            "scheme": "sepa",
            "reference": "GB27ZZZSDDBARC0000007495895",
            "minimum_advance_notice": 3,
            "currency": "EUR",
            "address_line1": "338-346 Goswell Road",
            "address_line2": null,
            "address_line3": null,
            "city": "London",
            "region": null,
            "postal_code": "EC1V 7LQ",
            "country_code": "GB",
            "email": "help@gocardless.com",
            "phone_number": "+44 20 7183 8674",
            "can_specify_mandate_reference": false
          }
        ],
        "verification_status": "successful",
        "links": {
          "default_gbp_payout_account": "BA0001RV7ZTN0Q",
          "default_eur_payout_account": "BA0001RV80ZED6",
          "default_sek_payout_account": "BA0001RV81GCJ8"
        }
      }
    ],
    "meta": {
      "cursors": {
        "before": null,
        "after": null
      },
      "limit": 50
    }
  }
}
```

- Create a Creditor bank account
    
    **POST : `/gcl/creditor/bank`**
    
```json
{
     "creditor_bank_accounts": {
         "currency": "EUR",
         "iban": "FR70 3000 2005 5000 0015 7845 Z02",
         "account_holder_name": "Sofacto",
         "country_code": "FR",
         "links": {
            "creditor": "CR00004REW8ZF1"
         }
     }
}
```

Return :

```json
{
  "response": {
    "creditor_bank_accounts": {
      "id": "BA0001T2KRVK12",
      "created_at": "2017-06-27T14:08:23.257Z",
      "account_number_ending": "02",
      "account_holder_name": "SOFACTO",
      "bank_name": "CREDIT LYONNAIS",
      "currency": "EUR",
      "country_code": "FR",
      "metadata": {},
      "enabled": true,
      "links": {
        "creditor": "CR00004REW8ZF1"
      }
    }
  }
}
```

- List a Creditor bank accounts
    
    **GET : `/gcl/creditors/bank`**

Return :

```json
{
  "response": {
    "creditor_bank_accounts": [
      {
        "id": "BA0001T2KRVK12",
        "created_at": "2017-06-27T14:08:23.257Z",
        "account_number_ending": "02",
        "account_holder_name": "SOFACTO",
        "bank_name": "CREDIT LYONNAIS",
        "currency": "EUR",
        "country_code": "FR",
        "metadata": {},
        "enabled": true,
        "links": {
          "creditor": "CR00004REW8ZF1"
        }
      },
      {
        "id": "BA0001RV80ZED6",
        "created_at": "2017-06-14T13:04:15.568Z",
        "account_number_ending": "06",
        "account_holder_name": "QUENTIN MILTGEN",
        "bank_name": "LA BANQUE POSTALE",
        "currency": "EUR",
        "country_code": "FR",
        "metadata": {},
        "enabled": true,
        "links": {
          "creditor": "CR00004REW8ZF1"
        }
      }
    ],
    "meta": {
      "cursors": {
        "before": null,
        "after": null
      },
      "limit": 50
    }
  }
}
```

###Mandates :
- Create a mandate

    **POST : `/gcl/mandate`**
```json
{
     "mandates": {
         "scheme": "sepa_core",
         "metadata": {
            "contract": "ABCD1234"
         },
         "links": {
             "customer_bank_account": "BA0001T0SYCFCV",
             "creditor": "CR00004REW8ZF1"
         }
     }
}
```
Return :
```json
{
  "response": {
    "mandates": {
      "id": "MD0001VVN1ZAGW",
      "created_at": "2017-06-27T14:26:41.094Z",
      "reference": "SOFACTO-Q6MFX4ZSBR",
      "status": "pending_submission",
      "scheme": "sepa_core",
      "next_possible_charge_date": "2017-06-30",
      "payments_require_approval": false,
      "metadata": {
        "contract": "ABCD1234"
      },
      "links": {
        "customer_bank_account": "BA0001T0SYCFCV",
        "creditor": "CR00004REW8ZF1",
        "customer": "CU000209DNA8HN"
      }
    }
  }
}
```

- Get a Mandate status if exists

    **GET : `/gcl/mandate/MD0001VVN1ZAGW`**

Return :
```json
{
  "response": {
    "mandates": {
      "id": "MD0001VVN1ZAGW",
      "created_at": "2017-06-27T14:26:41.094Z",
      "reference": "SOFACTO-Q6MFX4ZSBR",
      "status": "pending_submission",
      "scheme": "sepa_core",
      "next_possible_charge_date": "2017-06-30",
      "payments_require_approval": false,
      "metadata": {
        "contract": "ABCD1234"
      },
      "links": {
        "customer_bank_account": "BA0001T0SYCFCV",
        "creditor": "CR00004REW8ZF1",
        "customer": "CU000209DNA8HN"
      }
    }
  }
}
```

- Get a PDF Mandate link

    **GET : `/gcl/mandate/pdf/MD0001VVN1ZAGW`**

Return :
```json
{
  "response": {
    "mandate_pdfs": {
      "url": "https://mandate-previews-sandbox.gocardless.com/?token=eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..IKhitlRizR4ZOOLt.cGAWMihn63HoaoBj8d1k_bJOYvibEyJEFXpUjgM3t36hr7LZPvEh8RMsS_BTF_PiOsFyMKluGrR_xiuBQt5v53oOY4fwUkTlJ563tq5aO0_puXDNmoJYX_AwYQDy8SD_UBhYj9U0SAQFGyUU778HObaNtRdJP9-GxuywRpxCEvSMFvcKoqZBa3RlTcmYtHx9MvkRv8IsII4DfKZgi0ezYkrB9g3g2Ci0smseKc-l6RRazEC7D9gyvub_uFo4YaRc-qB-TpZy6puTWaL0xZLG4Gg5ZSJpxoDZh5wA8bNMULny6o6jLJsiHeiuwd4NKhEmm_qs7qoGNQ3NBsOe0SqjyHfMmOwC5vMEKJvEUO_EIZqpfqaGJvYC2aeTShAR7n99eS4SQenJL87wvsPE16XG7iOEOrldlc6bwZQxm-9tcq-c4RXmKVcPdkmxVNj2rLEV3WnrheepoyRMnn5eTEA-J8tLIj0CPt-43o7emvtybDcyb_zYKIJn6qOa-5QyKNBQfrv9Er4_UgUwsEvFPOSplscp4J2xeAvkJUtlT047GqEglTdUc7iuMZFbjriCfTgPzE3G0Fu_ygM7WDulm8SceD9mr8GdNon3sp5Rkho6vCm5N8gSeFnDaC8nMVvAiG9mPtCV9nvGmRst2YYFftFqwRVJLwiTo3t9.noRzlaECR-ell4Gf-anyBg",
      "expires_at": "2017-06-27T14:41:18.598Z"
    }
  }
}
```

###Payment :
- Create a Payment

    *amount is in cents : 100 = 1€

    **POST : `/gcl/payment`**
```json
{
     "payments": {
         "amount": 100,
         "currency": "EUR",
         "charge_date": "2018-05-19",
         "reference": "WINEBOX001",
         "metadata": {
             "order_dispatch_date": "2018-05-22"
         },
         "links": {
             "mandate": "MD0001VSV7TWK8"
         }
     }
 }
```
Return :
```json
{
  "response": {
    "payments": {
      "id": "PM0004YHRZK5SQ",
      "created_at": "2017-06-27T14:38:03.332Z",
      "charge_date": "2018-05-21",
      "amount": 100,
      "description": null,
      "currency": "EUR",
      "status": "pending_submission",
      "amount_refunded": 0,
      "reference": "WINEBOX001",
      "metadata": {
        "order_dispatch_date": "2018-05-22"
      },
      "links": {
        "mandate": "MD0001VSV7TWK8",
        "creditor": "CR00004REW8ZF1"
      }
    }
  }
}
```

- Check a Payment status

    **GET : `/gcl/payment/PM0004YHRZK5SQ`**

Return :
```json
{
  "response": {
    "payments": {
      "id": "PM0004YHRZK5SQ",
      "created_at": "2017-06-27T14:38:03.332Z",
      "charge_date": "2018-05-21",
      "amount": 100,
      "description": null,
      "currency": "EUR",
      "status": "pending_submission",
      "amount_refunded": 0,
      "reference": "WINEBOX001",
      "metadata": {
        "order_dispatch_date": "2018-05-22"
      },
      "links": {
        "mandate": "MD0001VSV7TWK8",
        "creditor": "CR00004REW8ZF1"
      }
    }
  }
}
```

- Create a Customer Flow ( Customer + Bank account + Mandate )

    *session_token should be a hash with salesforceid
    
    *redirect url should be personalized
    
    *send redirect_url in response to your client

    **POST : `/gcl/payment/makeflow`**
```json
{
  "redirect_flows": {
    "description": "Sofacto Subscription",
    "session_token": "SESS_wSs0uGYMISxzqOBq",
    "success_redirect_url": "https://example.com/pay/confirm",
    "prefilled_customer": {
      "given_name": "Paul",
      "family_name": "Fristache",
      "email": "fristach@ppda.com"
    }
  }
}
```
    
Return :
```json
{
  "response": {
    "redirect_flows": {
      "id": "RE0000GHNKQQEHVM3JJ5A4Z8PVF3BHRM",
      "description": "Sofacto Subscription",
      "session_token": "SESS_wSs0uGYMISxzqOBq",
      "scheme": null,
      "success_redirect_url": "https://example.com/pay/confirm",
      "created_at": "2017-06-27T14:47:50.639Z",
      "links": {
        "creditor": "CR00004REW8ZF1"
      },
      "redirect_url": "https://pay-sandbox.gocardless.com/flow/RE0000GHNKQQEHVM3JJ5A4Z8PVF3BHRM"
    }
  }
}
```

- Cancel a payment

**GET/POST : `/gcl/payment/cancel/:id`**


- Check the last events (WebHooks)

```json
{
    "webhook": [{
        "redirect_flows": {
            "id": "RE0000GN84QVE6M4CA2KNTVNWQPD8YSX",
            "description": "Sofacto Subscription",
            "session_token": "SESS_wSs0uGYMISxzqOBq",
            "scheme": null,
            "success_redirect_url": "https://sofacto.herokuapp.com/gclUser/payment/checkflow/",
            "created_at": "2017-06-30T07:23:51.333Z",
            "links": {
                "creditor": "CR00004REW8ZF1",
                "mandate": "MD0001WEV6E7FJ",
                "customer": "CU00020Y4HH8AG",
                "customer_bank_account": "BA0001TP4CVVQ8"
            }
        }
    }]
}
```

#Stripe Routes :

>For all your routes you need in your header :
```
Content-Type : application/json
```

>For /stripe/link you need theses keys in your header :
```
x-stripe-token : sk_test_iGa50Tgj1alUlPBWH8w1i8OM
x-sofactopay-token : 56341d8d-e6e4-4eee-9363-297e201b1624
x-sofactopay-user : sofacta
```
###Basic :

##Use the 'sofacta' user on heroku for now !

- Ask for a payment link

    **POST `/stripe/link`**
```json
{
    "amount":2490, // cents
    "currency":"eur", 
    "customerName":"Someone", // optional 
    "description":"Abonnement mensuel au journal Sofacta Mag"
}
```
Return : 

// a modifier pour tout mettre dans response ?
```json
{
  "response":true,
  "url":"localhost:5000/stripe/pay/a56cd56f-be53-40d2-8d90-3da7e01759a4"
}
```

TODO:
- Ask for payment status
    **GET `/stripe/payment/:id`**


###Admin :

- Create a new user // NE CRéé PAS CORRECTEMENT !

    **POST `/stripe/admin/addUser`**
```json
{ 
  "pass":"uNOZYe9yyHbvoS6vzjSzuTlHBW31PS", 
  "name":"jojo" 
}
```
Return : 
```json
{ 
  "response":true,
  "data":"user 'jojo' with token '8ed1b340-97ba-45f2-a8af-508e9e1d79d3' added "
}

```

- List all the users

    **POST `/stripe/admin/getUsers`**
    
```json
{ 
  "pass":"uNOZYe9yyHbvoS6vzjSzuTlHBW31PS"
}
```
Return : 
```json
{
"array":
    [{
    "_id":"594d28493bde80536cf40b21",
    "name":"sofacta",
    "spToken":"56341d8d-e6e4-4eee-9363-297e201b1624",
    "__v":0,
    "stripeKeys":{
        "pk":"pk_test_0yW122lFLdSOvxLIb1XrxiYY",
        "sk":"sk_test_iGa50Tgj1alUlPBWH8w1i8OM"},
    "created":"2017-06-23T14:40:09.103Z",
    "isActive":true,
    "linksGenerated":0
    }]
}
```

- Delete a user

    **POST `/stripe/admin/delUser`**

```json
{ 
  "pass":"uNOZYe9yyHbvoS6vzjSzuTlHBW31PS", 
  "name":"jojo" 
}
```

#Todo (STRIPE)

- CHANGER LES ROUTES VERS DU APPLICATION/JSON ONLY
- Ajouter une route avec config des parrams de l'user (delai expiration, clés etc)
- Séparer la création de client de la création du paiement (lier client au compte salesforce)
- Ajouter une initialisation pour comparer chaque client salesforce avec les clients stripe et les enregistrer si ça match
- Utiliser un middleware pour les routes admin

#Todo (GOCARDLESS)

- Ajouter BDD
- Separer webhook de payment

#Todo (All)

- Securiser la bdd (chiffrement reversible)
- Vérifier les erreurs de connexions vers les partenaires et bdd (eviter les crash)
- securiser les entrées (tests + try/catch)
- enregistrer qu'une fois les pk et sk
- Lier les BDD en une seule base pour tout les services
- Encapsuler les réponses pour la compatibilité APEX



Mongoose requests :
http://mongoosejs.com/docs/2.7.x/docs/finding-documents.html

GoCardLess Docs :
https://developer.gocardless.com/getting-started/api/introduction/
https://developer.gocardless.com/api-reference/

Stripe Docs : 
https://stripe.com/docs/api/node
https://stripe.com/docs/quickstart
