# 🚀 Guía de Instalación Arch Linux + Evangelion Setup  

> *"Sync Rate: 100% — Listo para el Third Impact (o al menos para codear)."*  

## 🔧 **Pre-instalación**  
### 1. Pre-instalacion 
- Descarga de la imagen de arch desde la pagina oficial "https://archlinux.org/download/"
- Creo la USB booteable con rufus "https://rufus.ie/es/"

### 2. Instalacion de Arch Linux
```bash
loadkeys es
```
  #Elegir idioma del sistema y asignar keymap
```bash 
nano /etc/locale.gen  #Descomentar es_AR.UTF-8
locale-gen
echo "LANG=es_AR.UTF-8" > /etc/locale.conf
localectl set-keymap es
```
  #Verificar si estas en UEFI o BIOS LEGACY
```bash
ls /sys/firmware/efi/efivars #Si aparecen archivos estan en UEFI, sino aparecen archivos estas en BIOS LEGACY
```
  #Configurar Zona Horaria
```bash
timedatectl list-timezones
timedatectl set-timezone "America/Mexico_City"  # Cambia por tu zona
timedatectl status
#Solucion si timedatectl no se aplica
hwclock --systohc  # Sincroniza el hardware clock
systemctl restart systemd-timesyncd  # Reinicia el servicio de hora
```
  #Comando para eliminar la tabla de particiones del disco
```bash
sgdisk --zap-all /dev/sdX  # Reemplaza sdX con tu disco (ej: /dev/nvme0n1 o /dev/sda)
```
  #Particionado para EUFI/EFI
```bash
fdisk -l #Comprobar la ruta del disco que vamos a usar 
cfdisk /dev/sda #sda es una ejemplo la ruta puede cambiar
```
  #Particion efi en fat32 y de 512MB, SWAP de 8Gb y la raiz del resto
  #Formateo y Montaje de las particiones
```bash
mkfs.fat -F32 /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
mkfs.ext4 /dev/sda3
mount /dev/sda3 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi  
```
#Instalacion de Pacstraps 
```bash
pacstrap /mnt base base-devel sudo nano
pacstrap /mnt linux linux-firmware linux-headers mkinitcpio networkmanager grub wpa_supplicant
```
#Generar fstab
-U es recomendado para la mayoria de casos -p es para casos especificos, el primero usa un Identificador unico universal para el montaje mientras que el segundo usa el nombre del dispositivo para montar las particiones 
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```
#Entrar al sistema instalado
```bash
arch-chroot /mnt
```
### 3. Post instalacion 
#Problemas con la Zona horaria
```bash
# 1. Configurar zona horaria (MÉTODO RECOMENDADO)
timedatectl set-timezone "America/Argentina" #saltar si esta bien el timezone

# 2. Sincronizar reloj del sistema
timedatectl set-ntp true
hwclock --systohc
```
#Problemas con Locale(idioma)
```bash
localectl status #para verificar que esta todo bien sino --->

nano /etc/locale.gen                          # Editar manualmente
locale-gen                                    # Generar locales
localectl set-locale LANG=es_MX.UTF-8         # Configurar idioma
localectl set-keymap es                       # Teclado en consola
localectl set-x11-keymap es                   # Teclado en GUI
```


