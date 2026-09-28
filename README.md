# MonitoringStack

Tramite vagrant deployare 3 virtual machine rocky


elasticsearch-host
monitoring-grafana
monitoring-prometheus


(Bonus, da verificare: esiste un modulo ansible per fare il deploy delle vm senza doverlo fare a mano prima con vagrant up? - in caso automatizziamolo nel ruolo)

Scrivere un ruolo ansible che:

Installi docker su tutte e 3 le macchine 
sulla vm elasticsearch-host deployare un container elasticsearch
sulla vm grafana deployare grafana
sulla vm prometheus deployare prometheus
su tutte e 3 le vm installare (non via docker) node-exporter


Requirements:

Settare l’heap di elasticsearch a 1G (https://www.elastic.co/docs/reference/elasticsearch/jvm-settings - fate il volume bind sul container di un conf file alla folder /usr/share/elasticsearch/config/jvm.options.d/ - -Xmx1G -Xms1G)
i volumi di grafana e prometheus devono essere persistenti
i 3 node exporter non devono essere installati tramite docker ma drete scaricarli da qualche parte (https://github.com/prometheus/node_exporter/releases/tag/v1.12.1 linux amd64 tar.gz) e creare manualmente (con ansible) un servizio systemd (example https://github.com/prometheus/node_exporter/blob/master/examples/systemd/node_exporter.service)
il prometheus deve fare scraping delle metriche dai 3 node exporter (example del prometheus.yml https://prometheus.io/docs/guides/node-exporter/ - paragrafo - settare 3 static targets uno per vm con node exporter) 
i 3 node exporter devono essere esposti su 3 porte differenti
Grafana deve avere una dashboard caricata - NODE EXPORTER FULL (https://grafana.com/grafana/dashboards/1860-node-exporter-full/) che potete anche caricare a mano una volta che avrete la ui disponibile


Bonus pro (FATELI SOLO QUANDO AVETE TERMINATO TUTTI I PUNTI SOPRA E IL RUOLO FUNZIONA - allora rimetteteci mano):

deploy delle vm vagrant tramite ansible (ammesso sia possibile, indagate https://galaxy.ansible.com/ui/dispatch/?pathname=%2Fcommunity%2Fvagrant)
il servizio systemd node-exporter NON ROOT (schiaffare il .service sotto  /home/utente/.config/systemd/utente && dare un occhiata al comando loginctl e l’opzione enable-linger)
la dashboard di grafana viene caricata in automatico tramite modulo (https://docs.ansible.com/projects/ansible/latest/collections/community/grafana/grafana_dashboard_module.html) o tramite chiamata CURL


Info:

node exporter espone sulla porta che scegliete un endpoint "/metrics". Puntate a quello tramite prometheus.
ricordatevi di aggiungere in grafana (manualmente) la datasource prometheus che punti al container prometheus.