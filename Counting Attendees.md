basically i just simply took the log files from the con days and threw these commands at them:

```sh
# filter out events that occurred before or after the dates, change the date range here using the proper timezone
awk '
BEGIN {
  start = "2025-07-04 15:00:00"
  end   = "2025-07-07 07:15:00"
}
{
  match($0, /\[([0-9]{2})\/([A-Za-z]+)\/([0-9]{4}):([0-9]{2}):([0-9]{2}):([0-9]{2})/, m)
  if (m[0]) {
    month_map["Jan"]="01"; month_map["Feb"]="02"; month_map["Mar"]="03"
    month_map["Apr"]="04"; month_map["May"]="05"; month_map["Jun"]="06"
    month_map["Jul"]="07"; month_map["Aug"]="08"; month_map["Sep"]="09"
    month_map["Oct"]="10"; month_map["Nov"]="11"; month_map["Dec"]="12"

    timestamp = m[3] "-" month_map[m[2]] "-" m[1] " " m[4] ":" m[5] ":" m[6]

    if (timestamp >= start && timestamp <= end) {
      print $0
    }
  }
}' access.log > access.log.date-filtered;

# filter out any lines that don't match cache-buster lines
sed -En '/\/schedule\/?\?cache-buster=/p' access.log.date-filtered > access.log.filtered

# remove everything from the log file but IP addresses
awk -F " " '{print $1}' access.log >> access.log.filtered.iponly

# remove any records that contain socket connections
sed -i.bak '/unix:/d' ./access.log.filtered.iponly

# sort file by IP address
sort access.log.filtered.iponly > access.log.filtered.iponly.sorted

# remove duplicate IP addresses
uniq access.log.filtered.iponly.sorted > access.log.filtered.iponly.sorted.uniq
```
