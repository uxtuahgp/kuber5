## Домашнее задание по теме "Хранение в Kubernetes" ##   

### Задание 1 ###  

```
lex@uxtu-note:~/Study/kuber5/kuber5$ kubectl exec -it vol-app-5676f457f9-ln6j7 --container mtool -- bash
vol-app-5676f457f9-ln6j7:/# ls -l /common
total 4
-rw-r--r--    1 root     root          1479 May 11 20:18 datefile.html
vol-app-5676f457f9-ln6j7:/# tail -f /common/datefile.html 
Mon May 11 20:18:41 UTC 2026
Mon May 11 20:18:46 UTC 2026
Mon May 11 20:18:51 UTC 2026
Mon May 11 20:18:56 UTC 2026
Mon May 11 20:19:01 UTC 2026
Mon May 11 20:19:06 UTC 2026
Mon May 11 20:19:11 UTC 2026
Mon May 11 20:19:16 UTC 2026
Mon May 11 20:19:21 UTC 2026
Mon May 11 20:19:26 UTC 2026
Mon May 11 20:19:31 UTC 2026
Mon May 11 20:19:36 UTC 2026
Mon May 11 20:19:41 UTC 2026
```  

