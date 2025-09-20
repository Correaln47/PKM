# General Structure
Areas - General/Big topics Maps of Contents
Projects - How and What i did
Resources - Whats and Whys


# Adding Videos
```html
<iframe width="720" height="480" src="https://drive.google.com/file/d/XXXXXXXXX/preview" frameborder="0" allowfullscreen></iframe>
```

# Adding captions
```html
<p style="color: grey; font-size: 16px; font-style: italic;">Caption Here.</p>
```

# Creating 2 columns (Only works with html formatting)
```html
<div style="display: flex; gap: 20px;">

<div markdown="1" style="flex: 1;">
Column 1
Contents

</div>

<div markdown="1" style="flex: 1;">
Column 2
Contents

</div>

</div>
```



# References Without a Document ⛓️‍💥
```dataview
TABLE without id 
out AS "Incomplete Links", file.link as "Origin"
FLATTEN file.outlinks as out
WHERE !(out.file) AND !contains(meta(out).path, "/")
SORT out ASC
```

# Documents to grow 🌳

```dataview
TABLE file.size AS "File Size (Bytes)"
WHERE file.size < 1000
SORT file.size ASC
```

# Most Outgoing🏆
```dataview
TABLE length(file.outlinks) AS "Outgoing Links"
FROM ""
SORT length(file.outlinks) DESC
LIMIT 10
```

# Most Popular🏆
```dataview
TABLE length(file.inlinks) AS "Incoming Links"
FROM !"00_Meta" 
SORT length(file.inlinks) DESC
LIMIT 10
```

