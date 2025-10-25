``` bash
for i in {1..5}; do \ echo -n "[?] Checking ID $i: "; \ curl -s -L -H "Host: blog.inlanefreight.local" "http://10.129.2.37/?author=$i" | grep -o "<title>.*</title>"; \ done
```