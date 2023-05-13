

![Kibana UI Setup 1](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/3d53fb3b-1d15-46c2-a808-7e36090e299e)

![Kibana UI Setup 1](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/d50fe68f-ff4b-4b3c-96db-d11c6ab89b01)

- Dans le terminal : 
```
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token --scope kibana
```

- copier / coller le resultat dans Kibana, puis valider 

![Kibana UI Setup 2](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/b9fe789c-ad82-4cf1-b178-f5d6fe144e7b)

- Dans le terminal : 
```
/usr/share/kibana/bin/kibana-verification-code
```

- copier / coller le resultat dans Kibana, puis valider 


![Kibana UI Setup 4](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/d8303515-e330-4a23-ae6b-92ad022a9078)


- Selectionner "Explore on my own" (ne pas sélectionner Add Integrations)

- Allez faire un tour dans `kibana.yml`:

⚠️ Remarquez que la configuration de Kibana a été effectuée automatiquement en dessous de la section: 
`# This section was automatically generated during setup.`
cette configuration a eu lieu lors de la phase de setup des token de sécurité elasticsearch juste après le login dans l'UI de Kibana. 

![Screenshot 2023-05-12 at 16 08 35](https://github.com/kplr-training/Elastic-Ingest/assets/123651815/8d686f56-0910-49c7-9e28-f7125adfc4be)
