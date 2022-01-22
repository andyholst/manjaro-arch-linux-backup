# manjaro-arch-linux-backup

Backup of Manjaro distro based on Arch Linux

## Clean up untracked git files easily

```
git clean -d -f
```

## Restore Linux system

```
find $(pwd)/manjaro -type f -name "*.gz.part*" -exec bash -c 'filename=$(basename {}) && newfile="${filename%.*}" && path=$(dirname {}) \
&& cat {} >>  ${path}/${newfile} && rm {}' \;
find $(pwd)/manjaro -type f -name "*.gz" -exec gzip -df \{\} \;
sudo chown root -R $(pwd)/manjaro
sudo rsync -aAX --delete --exclude={"/lost+found","/home"} $(pwd)/manjaro /mnt/root-system/
```

## Backup order with git

```
find $(pwd)/manjaro -type f -name "*.gz.part*" -exec bash -c 'filename=$(basename {}) && newfile="${filename%.*}" && path=$(dirname {}) \
&& cat {} >>  ${path}/${newfile} && rm {}' \;
find $(pwd)/manjaro -type f -name "*.gz" -exec gzip -df \{\} \;
sudo chown root -R $(pwd)/manjaro
sudo rsync -aAX --delete / --exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/swapfile","/lost+found","/home","/etc/NetworkManager/system-connections/*"} $(pwd)/manjaro
sudo chown $(whoami) -R $(pwd)/manjaro
find $(pwd)/manjaro/var/log -type f -name "*.log" -exec truncate --size 0 {} +
rm -fr $(pwd)/manjaro/var/lib/apt/lists/*
rm -fr $(pwd)/manjaro/var/cache/apt/archives
find $(pwd)/manjaro -type f -exec gzip -f \{\} \;
find $(pwd)/manjaro -type f -name "*.gz" -type f -size +100M -exec bash -c "split -b 100M {} {}.part && rm {}" \;

