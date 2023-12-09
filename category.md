---
layout: default
permalink: /category/
---

```bash
$ cat <span class="string">script.sh</span>
#!/bin/bash

# Navigate to the directory containing the categories
cd /home/gen0ne/categories

# List categories and count the number of folders
categories=$(ls -l | grep '^d' | awk '{print $9}')
total_folders=$(echo "$categories" | wc -l)
echo "total $total_folders"

# Loop through each category
echo "$categories" | while read -r category; do
    # Count the number of files inside each folder
    file_count=$(ls -l "$category" | grep '^-' | wc -l)
    echo "$category $file_count"
done
$ ./script.sh
total {{ site.tags | size }}
</p>
<p>
    {% assign list = site.tags | sort %}
    {% for category in list %}
        <a class="string" href="#{{ category[0] }}">{{ category[0] }}</a> {{ category[1].size }}<br />
    {% endfor %}
    {% assign list = nil %}
</p>
<p>
    {% assign taglist = site.tags | sort %}
    {% for category in taglist %}
        $ cat <span id="{{ category[0] }}" class="string">{{ category[0] }}</span><br />
        {% assign list = category[1] %}
        {% for post in list %}
            <a class="string" href="{{ post.url }}">{{ post.title }}</a><br />
        {% endfor %}
        <br />
        {% assign pages_list = nil %}
        {% assign group = nil %}
    {% endfor %}
    {% assign taglist = nil %}
</p>



