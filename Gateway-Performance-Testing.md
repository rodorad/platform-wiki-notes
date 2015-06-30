|# of instances|# messages per second|Size of app|# Concurrent connections|Notes|Haproxy |
|--------------|---------------------|-----------|------------------------|-----|--------|
|1|10|512&nbsp;MB|7,000|App runs out of memory.|1|
|2|10|512&nbsp;MB|15,000|App runs out of memory.|1|
|3|10|512&nbsp;MB|22,000|App runs out of memory.|1|
|4|10|512&nbsp;MB|27,000|Haproxy crashes. (Cannot assign address.)|1|
|5|10|512&nbsp;MB|27,000|Haproxy crashes. (Cannot assign address.)|1|
|1|10|512&nbsp;MB|7,000|App runs out of memory.|2|
|2|10|512&nbsp;MB|15,000|App runs out of memory.|2|
|3|10|512&nbsp;MB|22,000|App runs out of memory.|2|
|4|10|512&nbsp;MB|29,000|App runs out of memory.|2|