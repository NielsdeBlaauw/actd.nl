# About me

## Find me online

- [LinkedIn](https://www.linkedin.com/in/nielsdeblaauw/)
- [GitHub](https://github.com/NielsdeBlaauw)

## Latest posts
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
