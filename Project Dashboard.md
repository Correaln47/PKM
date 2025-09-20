```dataview
TABLE deadline, tags
FROM #project 
WHERE status = "in-progress" 
SORT deadline ASC
```
