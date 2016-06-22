---
title: CASAMAIL Restful post
keywords: casamail
summary: >-
  Objekt-Anfragen können in das CASAMAIL System durch diese
  RestFull-Schnittstelle erstellt werden
sidebar: casagateway_sidebar
permalink: /casamail-rest-post/
folder: casagateway
published: true
---


## Wie funktioniert es?

Anstatt eine E-Mail zu versenden soll hier ein POST request zu casamail.ch gemacht werden. Diese Anfrage wird dann in der casamail DB abgespeichert und CASAMAIL kann es von dort aus mehrfach distribuieren.

## Welche Werte sind möglich

Folgend finden Sie eine Übersicht-Liste aller Werte die eine `msg` (Nachricht) enthalten kann:

`x`: Wert kann nicht bestimmt werden.

`Fett`: Pflichtfeld

key                            | Mögliche Werte                      |   | Beschreibung
------------------------------ | ----------------------------------- | - | -------------------------------------------
id                             |                  -                  | x | Primärschlüssel wird automatisch genneriert
sent                           |                  -                  | x | Datetime Sende-Datum
status                         |                  -                  | x | NICHT BENUTZT
message                        |     <p>Ich bin eine Nachricht</p>   |   | Nachricht in HTML (Falls angegeben bitte `message_plain` frei lassen)
message_plain                  |        Ich bin eine Nachricht       |   | Nachricht in Plain (Falls angegeben bitte `message` frei lassen)
extra_data                     |{"acquiredThrough":"Suchmaschienen"} |   | Freier JSON string mit key value pairs. Sehe vorgeschlagene Werte unten
lang                           |       [de, en, fr, it, ... ]        |   | 2 Stelliger ISO kürzel der Nachrichts-Srache
origin                         |                  -                  | x | NICHT BENUTZT
property_reference             |             123.456.789             |   | Objekt referenz-nummer diese wird von Makler-Softwares angegeben um das Objekt zu identifizieren
gender                         |              [0, 1, 2]              |   | Absender: 0 = unbekannt, 1 = Mänlich, 2 = Weiblich
firstname                      |                 Max                 |   | Absender Vorname
lastname                       |                Muster               |   | Absender Nachname
legal_name                     |            Max Muster GmbH          |   | Absender Firmenname
street                         |           Musterstrasse 21          |   | Absender Postversand Strasse
postal_code                    |                1234                 |   | Absender Postversand PLZ/ZIP 
locality                       |               Ortingen              |   | Absender Postversand Ort/Stadt
country                        |          [CH, DE, AT, ...]          |   | Absender Postversand ISO 2 Stellig Land
phone                          |           +41 71 123 45 67          |   | Absender Telefon
mobile                         |           +41 71 123 45 67          |   | Absender Mobiltelefon
fax                            |           +41 71 123 45 67          |   | Absender Fax
**email**                      |           email@domain.ch           |   | Absender E-Mail
direct_recipient_email         |           makler@immo-ag.ch         |   | Direktes E-Mail soll an diese E-Mail addresse versendet werden. Normalerweise wird hier der Ansprechpartner des Objektes eingetragen.
send_cc_to_sender              |                  -                  |   | NICHT BENUTZT
recipients_notified_status     |                  -                  | x | Zustand ob Personen benachrichtigt wurden
recipients_notified_at         |                  -                  | x | Zeit wann Personen benachrichtigt wurden
recipients_notified_emails     |                  -                  | x | E-Mails addressen die benachrichtigt wurden
property_street                |                  -                  |   | Strasse des Objektes
property_postal_code           |                  -                  |   | PLZ/ZIP des Objektes
property_locality              |                  -                  |   | Ort/Stadt des Objektes
property_type                  |             [rent, buy]             |   | Verkaufsart des Objektes
property_category              |       [CasaXML category slug]       |   | Kategorie des Objektes
property_country               |          [CH, DE, AT, ...]          |   | ISO 2 Land des Objetktes
property_rooms                 |                 3.5                 |   | Anzahl Zimmer des Objektes
property_price                 |                123456               |   | Verkauf/Miet-Preis des Objektes
**provider**                   |               immo-ag               |   | Kunden-Slug des Objektes 
**publisher**                  |             immo-portal             |   | Veröffentlicher/Portal 
    
## Beispiel PHP POST mit CURL

```php
<?php
	$data = $postdata;
	$data['email'] = $postdata['emailreal'];
	$data['provider'] = $customerid;
	$data['publisher'] = $publisherid;
	$data['lang'] = substr(get_bloginfo('language'), 0, 2);
	$data['property_reference'] = $this->getFieldValue('referenceId');
	$data['property_street'] = $this->getFieldValue('address_streetaddress');
	$data['property_postal_code'] = $this->getFieldValue('address_postalcode');
	$data['property_locality'] = $this->getFieldValue('address_locality');
	$data['property_country'] = $this->getFieldValue('address_country');
	$data['property_country'] = $this->getFieldValue('address_country');
	/* ... */
	
	$data_string = json_encode($data);                                                                                   
	                                                                                                                     
	$ch = curl_init('http://onemail.ch/api/msg');
	curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");                                                                     
	curl_setopt($ch, CURLOPT_POSTFIELDS, $data_string);                                                                  
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);                                                                      
	curl_setopt($ch, CURLOPT_HTTPHEADER, array(                                                                          
	    'Content-Type: application/json',                                                                                
	    'Content-Length: ' . strlen($data_string))                                                                       
	);
	curl_setopt($ch, CURLOPT_USERPWD,  "simpleauthuser:simpleauthpassword");
	                                                                                                                     
	$result = curl_exec($ch);
	$json = json_decode($result, true);
	if (isset($json['validation_messages'])) {
		echo '<p>'.print_r($json['validation_messages'], true).'</p>';
	}
?>
```
