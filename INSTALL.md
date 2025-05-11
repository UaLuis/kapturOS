# Інструкція з встановлення ОС

Це інструкція для встановлення операційної системи, використовуючи Alpine Linux як базу. Усі команди та кроки покроково описані для зручності.

## Кроки для інсталяції:

1. **Налаштування репозиторіїв**:

   Спочатку потрібно налаштувати репозиторії для Alpine, якщо вони ще не налаштовані:
   ```bash
   setup-apkrepos -cf && apk update && apk add nano lsblk e2fsprogs
   ```
2. **Створення середовища**:

   Створимо змінну і директорію для монтування диску
   ```bash
   export LFS=/mnt/lfs && mkdir -pv $LFS
   ```
3. **Створення і монтування диску**:

   На цьому етапі, Ви маєте поділити диск на котрому бажаєте встановити ОС, на два розділи - один для swap, інший для системи.
   Можна використовувати Fdisk. Х і ХХ - замініть на Ваші розділи.
   ```bash
   mkswap /dev/sdX && mkfs.ext4 /dev/sdXX && mount -t ext4 /dev/sdXX /mnt/lfs
   ```
4. **Завантження образу ОС**:

   Заходимо в домашню директорію і завантажуємо образ ОС через wget
   ```bash
   cd $HOME && wget server/install_image.tar.gz
   ```
5. **Розпокування образу ОС у LFS директорію**:

   Розпокувуємо образ у LFS, але перед тим входимо у директорію /mnt/lfs
   ```bash
   cd $LFS && tar -xpf $HOME/install_image.tar.xz
   ```
6. **Видалення непотрібних файлів**:

   Видаляємо непотрібні файлі
   ```bash
   rm -fr init boot/grub
   ```
7. **Монтування середовища chroot**:

   Монтуємо середовище
   ```bash
   mount -v --bind /dev $LFS/dev
   mount -vt devpts devpts $LFS/dev/pts
   mount -vt proc proc $LFS/proc
   mount -vt sysfs sysfs $LFS/sys
   mount -vt tmpfs tmpfs $LFS/run
   ```
8. **Входимо у середовище chroot**:

   Виконуємо вхід у chroot
   ```bash
   chroot $LFS /bin/bash
   ```
9. **Реагування /etc/fstab**:
    
    Тепер змініть /etc/fstab, змінивши там назви розділів, котрі Ви створили для ОС.
    [В поміч ця частина з книги Linux From Scratch](https://www.linuxfromscratch.org/lfs/view/12.3/chapter10/fstab.html)
10. **Встановлення grub**Ж
    Встановлюємо Grub на наш диск (не розділ а саме диск). Y - Ваш диск, котрий Ви ділили на розділи Х і ХХ
    ```bash
    grub-install /dev/sdY --target i386-pc
    ```
11. **Створення /boot/grub/grub.cfg**:
    
    Тепер Ви маєте створити через редактор Nano grub.cfg у /boot/grub/ .
    [Так само в поміч тут буде ця частина з книги Linux From Scratch](https://www.linuxfromscratch.org/lfs/view/12.3/chapter10/grub.html) - 10.4.4. Creating the GRUB Configuration File
12. **Вихід з chroot і перезапуск ОС**:
    
    Вихід з chroot
    ```bash
    exit
    ```

    Потім перезапуск
    ```bash
    reboot
    ```
    
    Якщо Ви встановлюєте на реальному залізі - то Ви маєте після команди reboot вимкнути з комп'ютера флешку на якій був Alpine Linux (Якщо Ви встановлювали через флешку).
    Якщо ж на віртуальній машині (Я встановлював на UTM For MacOS), то Вам треба прибрати образ Alpine Linux з CD/DVD.
    
## Попередження!
**Не тестувалося на інших програм для створення віртуальних машин, також не тестувалося на реальному залізі, тому пробуйте і навчайтесь :)**
