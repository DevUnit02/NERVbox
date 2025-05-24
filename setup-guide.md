# 🚀 Guía de Instalación Arch Linux + Evangelion Setup  

> *"Sync Rate: 100% — Listo para el Third Impact (o al menos para codear)."*  

## 🔧 **Pre-instalación**  
### 1. Pre-instalacion 
- Descarga de la imagen de arch desde la pagina oficial "https://archlinux.org/download/"
- Creo la USB booteable con rufus "https://rufus.ie/es/"

### 2. Instalacion de Arch Linux
- loadkeys es
  
  #Elegir idioma del sistema y asignar keymap
  
- nano /etc/locale.gen  #Descomentar es_AR.UTF-8
- locale-gen
- echo "LANG=es_AR.UTF-8" > /etc/locale.conf
- localectl set-keymap es

  #Verificar si estas en UEFI o BIOS LEGACY

- ls /sys/firmware/efi/efivars #Si aparecen archivos estan en UEFI, sino aparecen archivos estas en BIOS LEGACY

  #Comando para eliminar la tabla de particiones del disco

- sgdisk --zap-all /dev/sdX  # Reemplaza sdX con tu disco (ej: /dev/nvme0n1 o /dev/sda)

  #Particionado para EUFI/EFI

- fdisk -l #Comprobar la ruta del disco que vamos a usar 
- cfdisk /dev/sda #sda es una ejemplo la ruta puede cambiar 
