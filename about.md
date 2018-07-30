---
titles:
  en: About
  zh: 关于
  zh-Hans: 关于
  zh-Hant: 關於
key: page-about
skill:
  lans: C#:4,Java:4,JavaScript:3,PHP:2
  tools: Git:4,Agile Methodology:4,Docker:4,ElasticSearch/Logstash/Kibana:4,RDBMS(SQL/PG/MySQL):3,Travis/TeamCity:3
  vis: D3.js:4,Tableau:3,Mapbox:2
---

Hi, I'm Henry, a **Programmer** :computer:, a **Data Visualiser** :bar_chart:, a **Photographer** :camera: and an **Entrepreneur** :department_store:.

I feel comfortable collaborating in multicultural and diverse-background 🌏 environments, with plethora of skills I'm able to complete everything that's being tasked to me.

## Work

Company |Country |Title |Year
[Swansea University](http://www.swansea.ac.uk/){:target="\_blank"} |UK 🇬🇧 |Software Developer |Nov 2017 - **Present**
[Swansea University](http://www.swansea.ac.uk/){:target="\_blank"} |UK 🇬🇧 |Software Developer Intern |Jun 2017 - Sep 2017
[LeEco](https://www.letv.com/){:target="\_blank"} |China 🇨🇳 |Java Developer Intern |Apr 2015 – July 2015
[TiViCloud](http://www.tivicloud.cn/){:target="\_blank"} |China 🇨🇳 |Java Developer Intern |Sep 2014 – Oct 2014
[Singapore Land Authority](https://www.sla.gov.sg/){:target="\_blank"} |Singapore 🇸🇬 |Geospatial Intern| June 2014 – Sep 2014

## Skill

I know many skills across different proficiency levels.

<style>
  .m-tags > ul > * {
    margin-top: 0.2rem !important;
    margin-bottom: 0.2rem !important;
    list-style-type: none !important;
    margin-right: 0.25rem !important;
  }
</style>
<div class="site-tags js-tags">
<ul class="menu">
{%- assign skills = page.skill.lans | split: ',' -%}
{%- assign i = skills | size -%}
  {% for skill in skills %}
  {%- assign pair = skill | split: ':' -%}
    <li>
      <button type="button" class="button button--pill tag-button tag-button-{{ pair[1] }} js-article-tag" data-encode="{{pair[0] | strip | url_encode }}">
          <span>{{ pair[0] | strip }}</span><div class="tag-button__count">{{ pair[1] }}</div>
        </button>
    </li>
  {% endfor %}
</ul>
</div>

<div class="site-tags js-tags">
<ul class="menu">
{%- assign skills = page.skill.tools | split: ',' -%}
{%- assign i = skills | size -%}
  {% for skill in skills %}
  {%- assign pair = skill | split: ':' -%}
    <li>
      <button type="button" class="button button--pill tag-button tag-button-{{ pair[1] }} js-article-tag" data-encode="{{pair[0] | strip | url_encode }}">
          <span>{{ pair[0] | strip }}</span><div class="tag-button__count">{{ pair[1] }}</div>
        </button>
    </li>
  {% endfor %}
</ul>
</div>

<div class="site-tags js-tags">
<ul class="menu">
{%- assign skills = page.skill.vis | split: ',' -%}
{%- assign i = skills | size -%}
  {% for skill in skills %}
  {%- assign pair = skill | split: ':' -%}
    <li>
      <button type="button" class="button button--pill tag-button tag-button-{{ pair[1] }} js-article-tag" data-encode="{{pair[0] | strip | url_encode }}">
          <span>{{ pair[0] | strip }}</span><div class="tag-button__count">{{ pair[1] }}</div>
        </button>
    </li>
  {% endfor %}
</ul>
</div>

<div class="site-tags js-tags">
<ul class="menu">
{%- assign skills = page.skill.os | split: ',' -%}
{%- assign i = skills | size -%}
  {% for skill in skills %}
  {%- assign pair = skill | split: ':' -%}
    <li>
      <button type="button" class="button button--pill tag-button tag-button-{{ pair[1] }} js-article-tag" data-encode="{{pair[0] | strip | url_encode }}">
          <span>{{ pair[0] | strip }}</span><div class="tag-button__count">{{ pair[1] }}</div>
        </button>
    </li>
  {% endfor %}
</ul>
</div>

I host services on my debian cloud server that simplify and automate my life, you can [request for access](mailto:hi@henry.wang).

<div class="site-tags js-tags">
<ul class="menu">
{%- assign skills = page.skill.services | split: ',' -%}
{%- assign i = skills | size -%}
  {% for skill in skills %}
  {%- assign pair = skill | split: ':' -%}
    <li>
      <button type="button" class="button button--pill tag-button tag-button-{{ pair[1] }} js-article-tag" data-encode="{{pair[0] | strip | url_encode }}">
          <span>{{ pair[0] | strip }}</span><div class="tag-button__count">{{ pair[1] }}</div>
        </button>
    </li>
  {% endfor %}
</ul>
</div>

## Education

Institution |Country |Major |Year |Result
[Swansea University](http://www.swansea.ac.uk/){:target="\_blank"} |UK 🇬🇧 |Msc. Advanced Computer Science |2015 - 2017 |**_Distinction_**
[Nanyang Polytechnic](http://www.nyp.edu.sg/){:target="\_blank"} |Singapore 🇸🇬 |Dip. Information Technology |2012 - 2015 |GPA 3.69/4.00
[Stanford University](https://www.coursera.org/learn/machine-learning){:target="\_blank"}[^1]|US 🇺🇸 |Machine Learning|2016 |[Certificate](https://www.coursera.org/account/accomplishments/records/QE9LANFCQYVE){:target="\_blank"}
[Wharton School](https://www.coursera.org/specializations/wharton-entrepreneurship){:target="\_blank"}[^1]|US 🇺🇸 |Entrepreneurship|2016 |[Certificate 1](https://www.coursera.org/account/accomplishments/records/GCCLDS6M5JAS){:target="\_blank"} [Certificate 2](https://www.coursera.org/account/accomplishments/records/N99U8UYLY44T){:target="\_blank"}
[Princeton University](https://www.coursera.org/learn/algorithms-part1){:target="\_blank"}[^1]|US 🇺🇸 |Algorithms |2016 | -null pointer-[^2]

## Testimonial

- For my masters dissertation, my supervisors said:

  > #### This is perhaps the best taught MSc project I have ever seen.  It's excellent work.
  >
  > Dr. Robert S. Laramee, Associate Professor, Swansea University
  >
  > #### Of a very high standard is the handling of the special situations, with its many details, and the integration into visualisation methods. A complete and useful software tool has been produced.
  >
  > Dr. Oliver Kullmann, Associate Professor, Swansea University

- For my academic performance, my lecturers said:

  > #### He was very driven and strived hard in his study. He worked well with team and was able to achieve goals set out by the project.
  >
  > Mr. Law Chee Yong, Senior Lecturer, Nanyang Polytechnic
  >
  > #### He is hard working and has a positive attitude towards his studies. He has always scored good grades.
  >
  > Mr. Loh Kuan Pang, Senior Lecturer, Nanyang Polytechnic

- For my internship performance, my supervisors said:
  > #### He has demonstrated excellent learning attitude, vast knowledge and skills on programming. He also displayed initiative by constantly seeking feedback.
  >
  > Mr. Justin Chua, Principal GeoSpatial Consultant, Singapore Land Authority

## Volunteer

Organisation |Country |Contribution |Year
[Food from the Heart](https://www.foodheart.org/){:target="\_blank"} |Singapore 🇸🇬|A [web portal](https://www.foodheart.org/vportal/){:target="\_blank"} for Food Stock, Delivery Schedule and Volunteer Schedule Management [^3]| Apr 2014 – Sep 2014

## Contact

For more details, please [email me](mailto:hi@henry.wang.com) or visit my [LinkedIn profile](https://www.linkedin.com/in/wangqiru).

##### Footnote

[^1]: Course was taken on [Coursera](https://www.coursera.org/), a learning platform offers online courses from universities and organisations. As of Oct 2017, Coursera has more than 28 million registered users and more than 2,000 courses.
[^2]: This course does not offer a certificate upon completion.
[^3]: The project is still actively maintained by students from Nanyang Polytechnic.
