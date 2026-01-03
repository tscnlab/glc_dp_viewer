---
layout: default
title: Metadata
---

<h1>GLEAM Metadata Schema</h1>

<ul>
  {% assign schema = site.data["gleam-dp-profile"] %}
  {% assign props = schema.allOf[1].properties %}
  {% assign required = schema.allOf[1].required %}
  {% assign skip_keys = "study" | split: " " %}

  {% for item in props %}
    {% unless skip_keys contains item[0] %}
      <li>
        <strong>{{ item[0] }}</strong>
        {% if required contains item[0] %}<span style="color: red">*</span>{% endif %}<br>
        {{ item[1].description }}
      </li>
    {% endunless %}
  {% endfor %}
</ul>

<hr>

<h2 id="study">Study Metadata Fields</h2>

{% assign study_schema = site.data["study_schema"] %}

<table>
  <thead>
    <tr>
      <th>Field</th>
      <th>Description</th>
      <th>Type</th>
    </tr>
  </thead>
  <tbody>
    {% for field in study_schema.fields %}
      <tr>
        <td>
          <code>{{ field.name }}</code>
          {% if field.constraints and field.constraints.required %}<strong>*</strong>{% endif %}
        </td>
        <td>{{ field.description }}</td>
        <td><code>{{ field.type }}</code></td>
      </tr>
    {% endfor %}
  </tbody>
</table>
