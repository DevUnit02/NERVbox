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


localectl set-keymap es
#mas comando

```
  #Verificar si estas en UEFI o BIOS LEGACY
```bash
ls /sys/firmware/efi/efivars #Si aparecen archivos estan en UEFI, si no aparecen archivos estas en BIOS LEGACY
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
pacstrap /mnt base base-devel nano
pacstrap /mnt linux linux-firmware linux-headers mkinitcpio
networkmanager grub wpa_supplicant
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
#Idioma en sistema 
```bash
nano /etc/locale.gen  #Descomentar es_AR.UTF-8
locale-gen
export LANG=es_AR_UTF-8
echo "LANG=es_AR.UTF-8" > /etc/locale.conf
```
#Problemas con la Zona horaria
```bash
# 1. Configurar zona horaria (MÉTODO RECOMENDADO)
timedatectl set-timezone "America/Argentina" #saltar si esta bien el timezone

ln -sf /usr/share/zoneinfo/America/Argentina/Buenos_Aires /etc/localtime

# 2. Sincronizar reloj del sistema
timedatectl set-ntp true
hwclock --systohc
hwclock -u
```
#Problemas con Locale(idioma)
```bash
localectl status #para verificar que esta todo bien sino --->
echo "KEYMAP=es" > /etc/vconsole.conf      # Para consola

#Alternativa

nano /etc/locale.gen                          # Editar manualmente
locale-gen                                    # Generar locales
localectl set-locale LANG=es_MX.UTF-8         # Configurar idioma
localectl set-keymap es                       # Teclado en consola
localectl set-x11-keymap es                   # Teclado en GUI

locale-gen
echo "LANG=es_ES.UTF-8" > /etc/locale.conf
localectl set-x11-keymap es                # Para entorno gráfico
```
#crear PC 
```bash
echo nombre_de_pc > /etc/hostname
passwd root #Contraseña de root
useradd -m -g users -G wheel -s /bin/zsh nombre_de_usuario
passwd nombre_de_usuario
nano /etc/sudoers
#Descomentar %wwheel ALL=(ALL:ALL) ALL
```
#Descargamos mas paquetes
```bash
pacman -S dhcp dhcpcd networkmanager iwd
systemctl enable dhcpcd NetworkManager
pacman -S reflector
reflector --verbose --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```
#Grub 
```bash
ls /boot/
pacman -S grub efibootmgr os-prober 
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Arch
grub-install --target=x86_64-efi --efi-directory=/boot/efi --removable
```
#Grub configuracion
```bash
nano /etc/default/grub
GRUB_TIMEOUT=10
GRUB_DISABLE_OS_PROBER= true
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3"

grub-mkconfig -o /boot/grub/grub.cfg
```

#Pacman.conf
```bash
nano /etc/pacman.conf
# Misc options
Color
VerbosePkgLists
ParallelDownloads = 5
ILoveCandy

[multilib]
Include = /etc/pacman.d/mirrorlist
#Salir y guardar
pacman -Sy
```

#Carpetas
```bash
pacman -S xdg-user-dirs
xdg-user-dirs-update
ls /root/
su nombre_de_usuario -c "xdg-user-dirs-update"
```
#Instalacion de tipografias y otras cosas
```bash
pacman -S gnu-free-fonts ttf-hack ttf-inconsolata noto-fonts-emoji
pacman -S neofetch lsb-release git wget

exit
umount -R /mnt
lsblk
swapoff /dev/swap
reboot
```

#Instalacion de driver de video y audio 
```bash
sudo pacman -S mesa lib32-mesa vulkan-intel lib32-vulkan-intel xf86-video-intel
#Alternativa ----------------
pacman -S xf86-video-vesa xf86-video-fbdev #Driver graficos genericos si no funcionan los recomendados segun que marca tengas
-------------------------------
pacman -S alsa alsa_firmware alsa-utils alsa-plugins #Drivers predetermiandos de audio en linux
pacman -S pipewire pipewire-alsa pipewire-pulse pipewire-audio gst-plugin-pipewire
```

