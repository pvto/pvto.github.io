---
layout: page
title: Bleeding poetry
tagline: Experiments, code
---
{% include JB/setup %}

## Recent posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

### From me, hosted elsewhere

<ul class="posts">
  <li><span>06 Aug 2015</span> &raquo; <a href="http://dev.solita.fi/2015/08/06/quad-tree.html">Is there an optimal quad tree out there?</a></li>
  <li><span>17 Apr 2015</span> &raquo; <a href="http://dev.solita.fi/2015/04/17/ninja-berries.html">Continuous learning and skills development at my workplace</a></li>
  <li><span>26 Jan 2015</span> &raquo; <a href="http://dev.solita.fi/2015/01/26/monster-fighter.html">Monster fighter</a></li>
  <li><span>15 Oct 2014</span> &raquo; <a href="http://dev.solita.fi/2014/10/15/unit-tests-for-good.html">Unit tests, for good</a></li>
  <li><span>01 Sep 2014</span> &raquo; <a href="http://dev.solita.fi/2014/09/01/infographs-with-d3js.html">Why infographs have an edge?</a></li>
  <li><span>30 Jun 2014</span> &raquo; <a href="http://dev.solita.fi/2014/06/30/writing-compact-java.html">Writing Compact Java with functions and data</a></li>
  <li><span>01 Apr 2014</span> &raquo; <a href="http://dev.solita.fi/2014/04/01/on-library-communication.html">Communication in library design, a user's perpective</a></li>
  
</ul>

###Thanks for reading!
(You can contact me if you did not understand something or would like to toss up some points.)

