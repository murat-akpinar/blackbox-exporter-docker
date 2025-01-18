
# Blackbox Exporter ile Grafana Dashboard Kurulumu

Bu proje, **Prometheus Blackbox Exporter**'ı izlemek ve analiz etmek için Grafana'da bir dashboard otomatik olarak yüklemek amacıyla hazırlanmıştır. Dashboard otomatik olarak `Prometheus` veri kaynağı ile ilişkilendirilir ve Blackbox Exporter'ın HTTP probelerini görselleştirir.

---

## Proje Yapısı

```plaintext
blackbox-exporter-docker/
├── provisioning/
│   ├── dashboards/
│   │   └── dashboard.yml       # Dashboard provisioning yapılandırma dosyası
│   └── datasources/
│       └── datasource.yml      # Prometheus veri kaynağı provisioning dosyası
dashboards/
└── blackbox-exporter.json      # Dashboard tasarımını ve panelleri tanımlayan JSON dosyası
docker-compose.yml              # Docker servis tanımları
prometheus.yml                  # Prometheus konfigürasyon dosyası
```

---

## Kullanım Adımları

### 1. Depoyu Klonlayın
Projeyi bilgisayarınıza klonlayın:
```bash
git clone https://github.com/kullaniciadi/blackbox-exporter-docker.git
cd blackbox-exporter-docker
```

### 2. Prometheus ve Blackbox Exporter Konfigürasyonu
`prometheus.yml` dosyasını kendi hedeflerinize göre düzenleyin. Örnek hedefler şu şekilde olabilir:
```yaml
scrape_configs:
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
          - https://example.com
          - https://another-example.com
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: blackbox-exporter:9115
```

### 3. Docker Compose ile Servisleri Başlatın
Tüm servisleri başlatmak için aşağıdaki komutu çalıştırın:
```bash
docker-compose up -d
```

Bu işlem, aşağıdaki servisleri ayağa kaldırır:
- **Prometheus**: `http://localhost:9090`
- **Blackbox Exporter**: `http://localhost:9115`
- **Grafana**: `http://localhost:3000` (Varsayılan kullanıcı adı: `admin`, şifre: `admin`)

### 4. Grafana'da Dashboard'u Kontrol Edin
Grafana'ya giriş yaparak `Dashboards > Manage` altında Blackbox Exporter dashboard'unun yüklendiğini doğrulayın. 

Eğer otomatik olarak yüklenmemişse:
- **Dashboard JSON'u Elle Yüklemek:** Grafana'da `Dashboards > Import` seçeneğini kullanarak `dashboards/blackbox-exporter.json` dosyasını yükleyin.
- **Data Source Bağlantısını Kontrol Edin:** Dashboard panellerinin veri kaynağı olarak `Prometheus` seçildiğinden emin olun.

---

## Projenin Özellikleri
- **Otomatik Dashboard Provisioning**: `dashboard.yml` ile JSON dosyaları otomatik olarak yüklenir.
- **Prometheus Veri Kaynağı**: `datasource.yml` dosyasıyla Prometheus otomatik olarak tanımlanır.
- **Blackbox Exporter İzleme**: HTTP, TCP ve SSL probe sonuçlarını görselleştirir.

---

## Sık Karşılaşılan Sorunlar
1. **Dashboard'da Veri Görünmüyor:**
   - Prometheus veri kaynağının adı `Prometheus` olarak doğru ayarlanmış mı?
   - `prometheus.yml` dosyasındaki hedefler doğru yapılandırılmış mı?

2. **Dashboard Otomatik Yüklenmedi:**
   - `dashboard.yml` dosyasının doğru mount edildiğinden emin olun (`./grafana/provisioning:/etc/grafana/provisioning`).

3. **Blackbox Exporter'a Ulaşılamıyor:**
   - `docker-compose.yml` dosyasındaki port ayarlarını kontrol edin (`9115:9115`).

---


![Screenshout](/image/Screenshot_20250118.png)

---
# Kaynakça

- [Blackbox_Exporter Github](https://github.com/prometheus/blackbox_exporter)
- [Grafana Dashboard](https://grafana.com/grafana/dashboards/13659-blackbox-exporter-http-prober)