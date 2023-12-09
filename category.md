---
layout: default
permalink: /category/
---

<p>$ cat <span class="string">script.sh</span><br />
$ #!/bin/bash<br />
<br />
# Navigate to the directory containing the categories<br />
cd /home/gen0ne/categories<br />
<br />
# List categories and count the number of folders<br />
categories=$(ls -l | grep '^d' | awk '{print $9}')<br />
total_folders=$(echo "$categories" | wc -l)<br />
echo "total $total_folders"<br />
<br />
# Loop through each category<br />
echo "$categories" | while read -r category; do<br />
    # Count the number of files inside each folder<br />
    file_count=$(ls -l "$category" | grep '^-' | wc -l)<br />
    echo "$category $file_count"<br />
done<br />
<p>$ <span class="string">./script.sh</span><br />
total {{ site.tags | size }}</p>
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



