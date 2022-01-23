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
chmod -w manjaro/var/lib/nfs/rpc_pipefs
chmod -rw manjaro/var/lib/snapd/void
chmod -w manjaro/sys
chmod -w manjaro/srv/ftp
chmod -w manjaro/proc
chmod -w manjaro/etc/ca-certificates/extracted/cadir
sudo chown root -R $(pwd)/manjaro
sudo rsync -aAX --delete --exclude={"/lost+found","/home"} $(pwd)/manjaro /mnt/root-system/
```

## Backup order with git

```
find $(pwd)/manjaro -type f -name "*.gz.part*" -exec bash -c 'filename=$(basename {}) && newfile="${filename%.*}" && path=$(dirname {}) \
&& cat {} >>  ${path}/${newfile} && rm {}' \;
find $(pwd)/manjaro -type f -name "*.gz" -exec gzip -df \{\} \;
sudo chown root -R $(pwd)/manjaro
sudo pacman -Scc
sudo rsync -aAX --delete / \
--exclude={"/dev/*","/proc/*","/sys/*","/tmp/*","/run/*","/mnt/*","/media/*","/swapfile","/lost+found","/home","/etc/NetworkManager/system-connections/*","*.pem"} $(pwd)/manjaro
sudo chown $(whoami) -R $(pwd)/manjaro
find $(pwd)/manjaro/var/log -type f -name "*.log" -exec truncate --size 0 {} +
find manjaro -type d -exec touch {}/.gitkeep \; # First time you need to chmod specific empty directories to keep Linux directory structure in Git
find $(pwd)/manjaro -type f -exec gzip -f \{\} \;
find $(pwd)/manjaro -type f -name "*.gz" -type f -size +100M -exec bash -c "split -b 100M {} {}.part && rm {}" \;
```

## Nessesary to chmod specific empty directories to be able to keep directory structure in git first time

```
chmod +w manjaro/var/lib/nfs/rpc_pipefs
chmod +rw manjaro/var/lib/snapd/void
chmod +w manjaro/sys
chmod +w manjaro/srv/ftp
chmod +w manjaro/proc
chmod +w manjaro/etc/ca-certificates/extracted/cadir
```
