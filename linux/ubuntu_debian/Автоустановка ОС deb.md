# Автоустановка ОС deb
## Использование preseed-файла
### Установка необходимых пакетов

Для создания preseed-файла и генерации нового образа ISO вам необходимо установить несколько пакетов. Это можно сделать с помощью команд:

```
sudo apt install rsync genisoimage isolinux wget xorriso
sudo apt-get install -y debconf-utils
```

- `genisoimage` - утилита для создания образов ISO;
- `isolinux` - пакет, содержащий файлы для загрузочного меню;
- `wget` - утилита для загрузки файлов;
- `xorriso` - утилита для работы с образами CD/DVD;
- `debconf-utils` - пакет, содержащий инструменты для работы с debconf.

### Генерация preseed-файла

Чтобы сгенерировать preseed-файл, вы можете использовать команду:

```
sudo debconf-get-selections --installer > preseed.cfg
```

Эта команда сохраняет текущие настройки debconf для пакетов, установленных в вашей системе, в файл `preseed.cfg`. В дальнейшем этот файл будет использоваться для автоматической установки операционной системы.

### Генерация нового образа ISO

Для генерации нового образа ISO с использованием preseed-файла вы можете использовать специальный скрипт `preseed-creator.sh`:

```
sudo chmod +777 ./preseed-creator.sh
sudo ./preseed-creator.sh -i image.iso -p preseed.cfg -o image_preseed.iso
```

Здесь:

- `./preseed-creator.sh` - это имя скрипта;
- `-i image.iso` - это путь к исходному образу ISO, который вы хотите использовать для создания нового образа;
- `-p preseed.cfg` - это путь к preseed-файлу, который вы создали ранее;
- `-o image_preseed.iso` - это имя нового образа ISO, который будет создан на основе исходного образа и preseed-файла.

После выполнения этих команд вы получите новый образ ISO, который можно использовать для автоматической установки операционной системы

### Скрипт [preseed-creator.sh](https://framagit.org/fiat-tux/hat-softwares/preseed-creator/-/blob/main/preseed-creator)
```bash
#!/bin/bash

function usage {
    cat <<EOF
Preseed Creator (c) Luc Didry 2017, WTFPL
./preseed_creator.sh [options]
    Options:
        -i <image.iso>              ISO image to preseed. If not provided, the script will download and use the latest Debian amd64 netinst ISO image
        -o <preseeded_image.iso>    output preseeded ISO image. Default to preseed_creator/debian-netinst-latest-preseed.ISO
        -p <preseed_file.cfg>       preseed file. If not provided, the script will put "d-i debian-installer/locale string fr_FR" in the preseed.cfg file
        -w <directory>              directory used to work on ISO image files. Default is a temporary folder in /tmp
        -x                          Use xorriso instead of genisoimage, to create an iso-hybrid
        -d                          download the latest Debian amd64 netinst ISO image in the current folder
        -g                          download the latest Debian stable example preseed file into preseed_example.cfg and exit
        -v                          activate verbose mode
        -h                          print this help and exit
EOF
    exit
}

function get_env_vars {
    if [[ -e /etc/environment ]]
    then
        [[ "$VERBOSE" == 1 ]] && echo 'Getting env vars from /etc/environment'
	while read -r line; do
	    if [[ "${line:0:1}" != '#' ]]; then
		export "${line?}"
	    fi
	done < /etc/environment
    fi
}

function download_latest_iso {
    ONLY_DOWNLOAD=$1
    if [ "$ONLY_DOWNLOAD" == 1 ]
    then
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Getting Debian GPG keys                     [========>                     ](25%)\r'
    else
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Getting Debian GPG keys                     [>                             ](0%)\r'
    fi
    if [[ "$VERBOSE" == 1 ]]
    then
        echo "Getting Debian GPG keys"
    fi
    for i in F41D30342F3546695F65C66942468F4009EA8AC3 DF9B9C49EAA9298432589D76DA87E80D6294BE9B 10460DAD76165AD81FBC0CE9988021A964E6EA7D
    do
        [[ "$VERBOSE" == 1 ]] && echo "Checking key $i"
        if ! (gpg --list-keys $i > /dev/null 2>&1)
        then
            [[ "$VERBOSE" == 1 ]] && echo "Key $i not in keyring, getting it from keyring.debian.org"
            gpg --keyserver keyring.debian.org --recv-keys 0x$i > /dev/null 2>&1
        fi
    done

    if [ "$ONLY_DOWNLOAD" == 1 ]
    then
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Downloading latest Debian amd64 netinst ISO [==============>               ](50%)\r'
    else
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Downloading latest Debian amd64 netinst ISO [===>                          ](10%)\r'
    fi
    [[ "$VERBOSE" == 1 ]] && echo "Parsing https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/ to fetch the latest netinst ISO file"
    LATEST=$(wget -q -O - https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/ | grep "netinst.iso" | grep -v "debian-mac" | grep -v "debian-edu" | sed -e 's@.*a href="\([^"]*\)".*@\1@')
    export LATEST
    [[ "$VERBOSE" == 1 ]] && echo "The latest netinst ISO file is $LATEST"
    wget "$WGETOPT" https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/"$LATEST" -O debian-netinst-latest.iso

    wget "$WGETOPT" https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA512SUMS -O SHA512SUMS
    wget "$WGETOPT" https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA512SUMS.sign -O SHA512SUMS.sign

    if [ "$ONLY_DOWNLOAD" == 1 ]
    then
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Verifying GPG signature                     [======================>       ](75%)\r'
    else
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Verifying GPG signature                     [======>                       ](20%)\r'
    fi

    [[ "$VERBOSE" == 1 ]] && echo "Verifying GPG signature"
    if ! (gpg --verify SHA512SUMS.sign SHA512SUMS > /dev/null 2>&1)
    then
        echo "Bad SHA512SUMS GPG signature. Aborting."
        exit 1
    fi

    if [ "$ONLY_DOWNLOAD" == 1 ]
    then
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Veryfying sha512sum                         [=============================>](100%)\r'
    else
        [[ "$VERBOSE" == 0 ]] && echo -ne 'Veryfying sha512sum                         [=========>                    ](30%)\r'
    fi
    [[ "$VERBOSE" == 1 ]] && echo "Fixing SHA512SUMS file"
    sed -e "s@${LATEST}@debian-netinst-latest.iso@" -i SHA512SUMS

    [[ "$VERBOSE" == 1 ]] && echo "Veryfying the sha512sum of the ISO file"
    if ! (sha512sum --ignore-missing -c SHA512SUMS > /dev/null 2>&1)
    then
        echo "Bad ISO checksum. Aborting."
        exit 1
    fi

    if [ "$ONLY_DOWNLOAD" == 1 ]
    then
        exit 0
    fi
}

INPUT=""
PRESEED=""
MYPWD=$(pwd)
OUTPUT=""
XORRISO=""
VERBOSE=""
WGETOPT="-q"
TMPDIR=""
DOWNLOAD_NETINST=""
DOWNLOAD_PRESEED=""

while getopts ":i:o:p:xdgvw:h" opt; do
    case $opt in
        i)
            INPUT=$OPTARG
            ;;
        o)
            OUTPUT=$OPTARG
            ;;
        p)
            PRESEED=$OPTARG
            ;;
        x)
            XORRISO='yes'
            ;;
        d)
	    DOWNLOAD_NETINST='yes'
            ;;
        g)
	    DOWNLOAD_PRESEED='yes'
            ;;
        v)
            VERBOSE=1
            WGETOPT=""
            ;;
        w)
            TMPDIR=$OPTARG
            if [[ ! -e $TMPDIR ]]
            then
                mkdir -p "$TMPDIR" || (echo "Unable to create $TMPDIR. Aborting." && exit 1)
            elif [[ ! -d $TMPDIR ]]
            then
                echo "$TMPDIR is not a directory. Aborting."
                exit 1
            fi
            ;;
        h)
            usage
            ;;
        \?)
            echo "Invalid option: -$OPTARG" >&2
            usage
            ;;
    esac
done

get_env_vars

if [[ -n "$DOWNLOAD_NETINST" ]]
then
    echo "Downloading latest Debian amd64 netinst ISO image"
    download_latest_iso 1
fi
if [[ -n "$DOWNLOAD_PRESEED" ]]
then
    echo "Downloading latest Debian stable example preseed file into preseed_example.cfg"
    if [[ "$VERBOSE" == 0 ]]
    then
        WGETOPT=""
    fi
    wget "$WGETOPT" http://www.debian.org/releases/stable/example-preseed.txt -O preseed_example.cfg
    echo "Done"
    exit
fi
if [[ -z "$TMPDIR" ]]
then
    TMPDIR=$(mktemp -p /tmp -d preseed-creator.XXXXXXXXXXX)
fi
cd "$TMPDIR" || (echo "Unable to go to $TMPDIR. Aborting." && exit 1)

if [[ -n $PRESEED ]]
then
    if [ "${PRESEED:0:1}" != / ]
    then
        PRESEED="${MYPWD}/${PRESEED}"
    fi
    if [[ ! -e $PRESEED ]]
    then
        echo "$PRESEED does not exists. Aborting."
        exit 1
    fi
    if [[ ! -r $PRESEED ]]
    then
        echo "$PRESEED is not readable. Aborting."
        exit 1
    fi
fi

if [[ -n $OUTPUT ]]
then
    if [ "${OUTPUT:0:1}" != / ]
    then
        OUTPUT="${MYPWD}/${OUTPUT}"
    fi
else
    OUTPUT="${MYPWD}/debian-netinst-latest-preseed.iso"
fi

if [[ -z $INPUT ]]
then
    echo "No ISO image provided, will download the latest Debian amd64 netinst ISO image"
    download_latest_iso 0

    INPUT="debian-netinst-latest.iso"
else
    if [ "${INPUT:0:1}" != / ]
    then
        INPUT="${MYPWD}/${INPUT}"
    fi
    if [[ ! -e $INPUT ]]
    then
        echo "$INPUT does not exists. Aborting."
        exit 1
    fi
    if [[ ! -r $INPUT ]]
    then
        echo "$INPUT is not readable. Aborting."
        exit 1
    fi
fi

[[ "$VERBOSE" == 0 ]] && echo -ne 'Mounting ISO image                          [===========>                  ](40%)\r'
[[ "$VERBOSE" == 1 ]] && echo "Mounting ISO image $INPUT"
mkdir loopdir -p
if ! (mount -o loop "$INPUT" loopdir > /dev/null 2>&1)
then
    echo "Error while mounting the ISO image. Aborting."
    exit 1
fi

mkdir cd
[[ "$VERBOSE" == 0 ]] && echo -ne 'Extracting ISO image                        [==============>               ](50%)\r'
[[ "$VERBOSE" == 1 ]] && echo "Extracting ISO image"
rsync -a -H --exclude=TRANS.TBL loopdir/ cd
[[ "$VERBOSE" == 0 ]] && echo -ne 'Umounting ISO image                         [=================>            ](60%)\r'
[[ "$VERBOSE" == 1 ]] && echo "Umounting ISO image"
umount loopdir

[[ "$VERBOSE" == 0 ]] && echo -ne 'Hacking initrd                              [====================>         ](70%)\r'
[[ "$VERBOSE" == 1 ]] && echo -e "Hacking initrd\nGetting initrd.gz content"
mkdir irmod -p
(
cd irmod || exit 1
if ! (gzip -d < ../cd/install.amd/initrd.gz | cpio --extract --make-directories --no-absolute-filenames 2>/dev/null)
then
    echo "Error while getting ../cd/install.amd/initrd.gz content. Aborting."
    exit 1
fi

[[ "$VERBOSE" == 0 ]] && echo -ne 'Disabling menu graphic installer              [=======================>      ](80%)\r'
[[ "$VERBOSE" == 1 ]] && echo "Disabling menu graphic installer"
if ! (sed -i 's/include gtk.cfg//g' ../cd/isolinux/menu.cfg 2>/dev/null)
then
    echo "Error while disabling graphic menu installer in ../cd/isolinux/isolinux.cfg. Aborting."
    exit 1
fi

if [[ -z $PRESEED ]]
then
    [[ "$VERBOSE" == 1 ]] && echo "Creating a simple preseed.cfg file"
    echo "d-i debian-installer/locale string fr_FR" > preseed.cfg
else
    cp "$PRESEED" preseed.cfg
fi
[[ "$VERBOSE" == 1 ]] && echo "Putting new content into initrd.gz"
if ! (find . | cpio -H newc --create 2>/dev/null | gzip -9 > ../cd/install.amd/initrd.gz 2>/dev/null)
then
    echo "Error while putting new content into ../cd/install.amd/initrd.gz. Aborting."
    exit 1
fi
)

rm -rf irmod/

[[ "$VERBOSE" == 0 ]] && echo -ne 'Fixing md5sums                              [========================>     ](85%)\r'
[[ "$VERBOSE" == 1 ]] && echo "Fixing md5sums"
read -ra files < <(find cd/ -follow -type f 2>/dev/null)
if ! ( md5sum "${files[0]}" > md5sum.txt 2>/dev/null )
then
    echo "Error while fixing md5sums. Aborting."
    exit 1
fi


[[ "$VERBOSE" == 0 ]] && echo -ne 'Creating preseeded ISO image                [==========================>   ](90%)\r'
[[ "$VERBOSE" == 1 ]] && echo "Creating preseeded ISO image"
if [[ -z $XORRISO ]]
then
    if ! (genisoimage -quiet -o "$OUTPUT" -r -J -no-emul-boot -boot-load-size 4 -boot-info-table -b isolinux/isolinux.bin -c isolinux/boot.cat ./cd > /dev/null 2>&1)
    then
        echo "Error while creating the preseeded ISO image. Aborting."
        exit 1
    fi
else
	if ! (xorriso -as mkisofs \
		-quiet \
		-o "$OUTPUT" \
		-isohybrid-mbr /usr/lib/ISOLINUX/isohdpfx.bin \
		-c isolinux/boot.cat \
		-b isolinux/isolinux.bin \
		-no-emul-boot -boot-load-size 4 -boot-info-table \
		-eltorito-alt-boot \
		-e boot/grub/efi.img \
		-no-emul-boot \
		-isohybrid-gpt-basdat \
        ./cd /dev/null 2>"$1")
    then
        echo "Error while creating the preseeded ISO image. Aborting."
        exit 1
    fi
fi

rm -rf "$TMPDIR"

[[ "$VERBOSE" == 0 ]] && echo -ne 'Preseeded ISO image created                 [==============================](100%)\r'
[[ "$VERBOSE" == 1 ]] && echo "Preseeded ISO image created"
echo -e "\nYour preseeded ISO image is located at $OUTPUT"

```