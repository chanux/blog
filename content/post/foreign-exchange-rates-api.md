---
title: "Foreign Exchange Rates Api"
date: 2018-12-22T23:50:00+08:00
draft: false
---

Once upon a time I wanted to keep track of foreign exchange rates and probably
do something useful with it.  I looked for any APIs which would ideally present
data in JSON. Unsurprisingly I didn't find any. So I had to build it myself.

Now, the only way to do this was scraping exchange rates pages of the banks
I am interested in. Even though I knew this scraping thing is an ugly, dirty
thing, I was too excited about the idea to just give up on it.

I picked golang since I really wanted to use it more and improve my golang. But
I wouldn't have been able to do it without the excellent
[Goquery](https://github.com/puerkitobio/goquery) library which is reminiscent
of good old jQuery.

This is how it works.

`curl http://xr.chanux.me/combank/2018-12-20`

```json
{
  "date": "2018-12-20T00:00:00Z",
  "bank": "Commercial Bank Of Ceylon",
  "base_currency": "LKR",
  "rates": [
    {
      "currency": "US Dollars (USD)",
      "buying": "175.76",
      "selling": "184.00",
      "buying_tt": "178.00",
      "selling_tt": "184.00",
      "buying_c_d": "177.37",
      "selling_c_d": "184.00",
      "import_bills": "183.90",
      "unit": "1"
    },
    {
      "currency": "EURO (EUR)",
      "buying": "197.86",
      "selling": "210.36",
      "buying_tt": "201.72",
      "selling_tt": "210.36",
      "buying_c_d": "201.02",
      "selling_c_d": "210.36",
      "import_bills": "210.24",
      "unit": "1"
    }
  ]
}
```

Let me explain and expand on what you just saw. First, you query the API using
bank code and date. Following are the supported banks and their codes.

| Bank Code   | Exchange Rates page URL                                                             |
|-------------|-------------------------------------------------------------------------------------|
| boc         | https://www.boc.web.lk/ExRates                                                      |
| combank     | http://www.combank.lk/newweb/en/rates-tariffs/exchange-rates                        |
| dbs         | https://www.dbs.com.sg/personal/rates-online/foreign-currency-foreign-exchange.page |
| hnb         | https://www.hnb.net/exchange-rates                                                  |
| peoplesbank | https://www.peoplesbank.lk/foreign-exchange-rate                                    |
| sampath     | https://www.sampath.lk/en/exchange-rates                                            |
| seylan      | https://www.seylan.lk/exchange-rates                                                |

NOTE: DBS is Development Bank of Singapore

In currency data, *buying* and *selling* fields are for the respective exchange
rates for **currency notes**. tt in buying_tt/selling_tt means **Telegraphic
Transfer**. Similarly, *buying_c_d/selling_c_d* is for rates for **Travellers
Cheques** and **Demand Drafts**. These seem to be also called OD (On Demand).

I have tried to come up with a common format that works across different banks.
Differnet banks present varying amounts of data sometimes with different
terminology. For example HNB presents very little data and they don't care to
mention the date (So I had to find another way to find out the date). There is
a chance I misunderstood something or made a mistake in representing something.
Please report if you come across such issues.

I hope this is useful to somene. It would be amazing to have a nice unified UI
for excnage rates across banks. I guess this API would make things easy for
anyone who wants to take a shot at building it :).

PS: My scraper has been breaking due to date format change of several banks so
there is a gap in data. I have stood up a newer/improved version at
`http://betaxr.chanux.me`. I will sort things out and probably move over old
data and update here.
