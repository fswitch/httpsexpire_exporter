# HTTPS Expire exporter

Prometheus exporter for SSL certificates of web sites.  
It gets seconds until the expiration date for prometheus TSDB.  
This exporter can get certificates of web sites resolved by public DNS and also by specified IP.  
It can be helpfull when you need to monitor expiration date of certificates behind the proxies.  
____
## Config (httpsexpire_exporter.yml):
```yml
# IP address for local server
source_ip_address: 127.0.0.1

# IP port for local server
source_ip_port: 9959

# Time in seconds betbeen certificate queries
query_time: 7200

# Show http logs (0/1)
http_log_show: 0

# Domains configuration
hosts:
  google.com:            # (required) title for site
    domain: google.com   # (required) site's domain
    ip: 142.250.180.206  # (optional) ip for site (if it's not public, e.g. behind the cloudflare)
    port: 443            # (optional) port of site (443 as default)
  gmail.com:
    domain: gmail.com
```
  
### Prometheus scraping
```
scrape_configs:
  - job_name: httpsexpire
    scrape_interval: 6h
    static_configs:
      - targets: [ '127.0.0.1:9959' ]
```
  
### Prometheus alert rules
```
groups:
- name: alert_rules
  rules:
    - alert: CertExpire
      expr: cert_expire{instance=~"127.0.0.1:9959",job=~"httpsexpire"} < 172800
      labels:
        severity: "critical"
      annotations:
        summary: "Site {{ $labels.title }} expires soon"
```
  
