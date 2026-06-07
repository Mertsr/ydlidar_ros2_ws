# YDLidar ROS2 Workspace

> Ydlidar X2 package for ROS2 Humble - Python 3.10 & ROS2 Humble için hazırlanmış kurulum rehberi

---

## 📋 Gereksinimler

- **Ubuntu**: 22.04 LTS
- **Python**: 3.10+
- **ROS2**: Humble
- **YDLidar X2** sensörü (USB bağlantı)

---

## 🚀 Hızlı Kurulum

### 1. ROS2 Humble Kurulumu

```bash
# Locale'i ayarla
locale  # UTF-8 olduğundan emin ol

# ROS2 kaynaklarını ekle
sudo apt update && sudo apt install curl gnupg lsb-release -y
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  | sudo apt-key add -
sudo sh -c 'echo "deb [arch=$(dpkg --print-architecture)] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ros2-latest.list'

# Kur
sudo apt update
sudo apt install ros-humble-desktop -y

# Her terminalde yükle
source /opt/ros/humble/setup.bash
```

**Permanent yapma:**
```bash
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

### 2. YDLidar SDK Kurulumu

```bash
# SDK'yı klonla
cd ~
git clone https://github.com/YDLIDAR/YDLidar-SDK.git
cd YDLidar-SDK

# Build et ve yükle
mkdir build
cd build
cmake ..
make
sudo make install
```

### 3. Build Tools Yükleme

```bash
sudo apt install python3-colcon-common-extensions -y
sudo apt install ros-humble-launch* -y
```

### 4. Workspace Kurulumu

```bash
# Workspace oluştur
mkdir -p ~/ydlidar_ros2_ws/src
cd ~/ydlidar_ros2_ws

# Bu repoyu klonla
git clone https://github.com/Mertsr/ydlidar_ros2_ws.git .
```

### 5. Workspace Build Et

```bash
cd ~/ydlidar_ros2_ws
colcon build --symlink-install
```

**Başarılı build sonrası:**
```bash
source install/setup.bash
```

### 6. Seri Port Ayarı (Opsiyonel)

```bash
# Setup script'i çalıştır
chmod +x src/ydlidar_ros2_driver/startup/*
sudo sh src/ydlidar_ros2_driver/startup/initenv.sh

# YDLidar'ı USB'den çıkarp tekrar tak
```

---

## ▶️ Çalıştırma

### Başlat
```bash
source ~/ydlidar_ros2_ws/install/setup.bash
ros2 launch ydlidar_ros2_driver ydlidar_launch.py
```

### RViz ile Görselleştir
```bash
ros2 launch ydlidar_ros2_driver ydlidar_launch_view.py
```

### Tarama Verilerini İnceleme
```bash
source ~/ydlidar_ros2_ws/install/setup.bash
ros2 topic echo /scan
```

---

## 🔧 Sorun Giderme

### ❌ "Permission denied" `/dev/ttyUSB0`
```bash
sudo usermod -a -G dialout $USER
sudo reboot
```

### ❌ "YDLidar-SDK bulunamadı" hatası
SDK'nın yüklü olup olmadığını kontrol et:
```bash
pkg-config --modversion ydlidar_sdk
```

Eğer bulunamazsa SDK'yı tekrar yükle:
```bash
cd ~/YDLidar-SDK/build
sudo make install
```

### ❌ `colcon build` başarısız oldu
```bash
# Eski build dosyalarını sil
rm -rf build install log

# Tekrar dene
colcon build --symlink-install
```

### ❌ Cihaz bağlanmıyor
```bash
# Bağlı cihazları kontrol et
lsusb | grep -i lidar

# Seri portları listele
ls /dev/ttyUSB*

# Cihazı yeniden tak
```

---

## 📚 ROS2 Topics & Services

### Yayınlanan Topic
| Topic | Tip |
|-------|-----|
| `/scan` | `sensor_msgs/LaserScan` |

### Services
| Service | İşlev |
|---------|-------|
| `/start_scan` | LIDAR başlat |
| `/stop_scan` | LIDAR durdur |

**Örnek:**
```bash
# Durdur
ros2 service call /stop_scan std_srvs/srv/Empty

# Başlat
ros2 service call /start_scan std_srvs/srv/Empty
```

---

## 📁 Proje Yapısı

```
ydlidar_ros2_ws/
├── src/
│   └── ydlidar_ros2_driver/
│       ├── src/                    # C++ kaynak kodları
│       ├── launch/                 # ROS2 launch dosyaları
│       ├── params/                 # Konfigürasyon (ydlidar.yaml)
│       ├── startup/                # Sistem setup scriptleri
│       ├── CMakeLists.txt
│       └── package.xml
├── install/                        # Build çıktısı
├── build/                          # Build dosyaları
└── README.md
```

---

## ⚙️ Konfigürasyon

Parametreler: `src/ydlidar_ros2_driver/params/ydlidar.yaml`

| Parametre | Açıklama | Varsayılan |
|-----------|----------|-----------|
| `port` | Seri port | `/dev/ttyUSB0` |
| `baudrate` | Baud hızı | `230400` |
| `frame_id` | TF frame | `laser_frame` |
| `frequency` | Tarama frekansı (Hz) | `10.0` |
| `angle_min/max` | Açı aralığı | -180 / 180 |
| `range_min/max` | Mesafe aralığı | 0.01 / 64.0 |

---

## 💡 İpuçları

- Her yeni terminal penceresi için `source install/setup.bash` çalıştır
- RViz'de "Fixed Frame" = `laser_frame` olmalı
- LIDAR'ı takmadan önce sürücüyü başlatma
- Seri port hızı (baudrate) 230400 olmalı

---

## 📝 Lisans

MIT License

---

**Hazırlayan:** Mertsr  
**Versiyon:** 1.0.1  
**ROS2:** Humble  
**Python:** 3.10+
