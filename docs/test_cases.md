# Test Cases - End-to-End BI Data Validation

Eseguiti il 04/03/2026 su notebook end_to_end_validation.ipynb

**TC01 - Riconciliazione revenue (source vs staging)**  
Precondizione: Dati caricati e joinati in staging  
Query: SUM(amount) source vs SUM(amount) staging  
Risultato atteso: valori identici  
Risultato ottenuto: source 4640.49 vs staging 7791.98 → MISMATCH (differenza 3151.49)  
Esito: FAIL (defect critico rilevato – possibile errore in ETL o dati sporchi)

**TC02 - Clienti senza vendite (orfani)**  
Precondizione: LEFT JOIN CRM → staging  
Risultato atteso: clienti in CRM ma non in vendite segnalati  
Risultato ottenuto: 4 clienti orfani (Marco Blu, Sara Rosa, Antonio Verde, Laura Arancione)  
Esito: PASS (defect reale rilevato e documentato)

**TC03 - Controllo duplicati ordini in source**  
Precondizione: GROUP BY order_id HAVING COUNT(*) > 1 su sap_sales  
Risultato atteso: 0 duplicati (dopo pulizia ETL)  
Risultato ottenuto: 0 duplicati  
Esito: PASS

**TC04 - Business rule High Value (>300)**  
Precondizione: Categoria calcolata in staging (amount > 300 → 'High Value')  
Risultato atteso: tutti ordini >300 categorizzati correttamente  
Risultato ottenuto: 5 ordini High Value trovati, tutti categorizzati 'High Value' → True  
Esito: PASS

**TC05 - Nessuna anomalia revenue per categoria (<100)**  
Precondizione: Aggregazione per order_category HAVING SUM(amount) < 100  
Risultato atteso: 0 righe (nessuna categoria troppo bassa)  
Risultato ottenuto: 0 righe ritornate  
Esito: PASS (nessuna anomalia rilevata)

Note:  
- Revenue mismatch in TC01 è il defect più grave → da indagare upstream (valori negativi? cliente inesistente? duplicati non gestiti?)  
- Tutti gli altri test PASS → validazione parziale superata.