basically i just simply took the log files from the con days and threw these commands at them:

```sh
sed '/GET \/schedule.html?cache-buster/!d' access.log > access.log.filtered
awk -F " " '{print $1}' access.log >> access.log.filtered.iponly
sed -i.bak '/unix:/d' ./access.log.filtered.iponly
sort access.log.filtered.iponly > access.log.filtered.iponly.sorted
uniq access.log.filtered.iponly.sorted > access.log.filtered.iponly.sorted.uniq
```
