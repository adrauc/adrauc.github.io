---
layout: archive
title: "Media"
permalink: /media/
author_profile: true
sitemap: false
---

{% include base_path %}

<script>
document.addEventListener("DOMContentLoaded", function () {
  document.body.classList.add("publications-page");
});
</script>

<div class="filter-row">
  <div class="filter-label">FORMAT:</div>
  <div class="filter-group" data-filter-group="format">
    <button type="button" class="pill is-active" data-value="all">All</button>
    <button type="button" class="pill" data-value="My Research in the Media">My Research in the Media</button>
    <button type="button" class="pill" data-value="Articles & Commentary">Articles & Commentary</button>
  </div>
</div>

<div class="filter-row">
  <div class="filter-label">TOPIC:</div>
  <div class="filter-group" data-filter-group="topic">
    <button type="button" class="pill is-active" data-value="all">All</button>
    <button type="button" class="pill" data-value="AI">AI</button>
    <button type="button" class="pill" data-value="Politics">Politics</button>
    <button type="button" class="pill" data-value="Disinformation">Disinformation</button>
    <button type="button" class="pill" data-value="Journalism">Journalism</button>
    <button type="button" class="pill" data-value="Science Communication">Science Communication</button>
    <button type="button" class="pill" data-value="Social Media">Social Media</button>
    <button type="button" class="pill" data-value="Public Opinion">Public Opinion</button>
  </div>
</div>

<div class="filter-meta">
  <span id="filterCount"></span>
  <button type="button" class="pill pill-clear" id="clearFilters">Clear</button>
</div>

<hr>

{% assign items = site.data.media | default: empty %}
{% assign items = items | sort: "year" | reverse %}

<div id="pubList">
{% if items.size > 0 %}
  {% for item in items %}

  <div class="pub-item"
       data-topic="{{ item.topic | escape }}"
       data-format="{{ item.format | escape }}">

      <div class="pub-year">
        {{ item.year | default: "—" }}
      </div>

      <div class="pub-main">
        <p class="pub-title"><strong>{{ item.title }}</strong></p>

        {% if item.authors and item.authors != "" %}
        <p class="pub-authors">
          {{ item.authors
             | replace: "Adrian Rauchfleisch", "<span class='pi-name'>Adrian Rauchfleisch</span>"
          }}
        </p>
        {% endif %}

        <p class="pub-journal">
          {{ item.outlet }}
        </p>

        <div class="pub-icons">
          {% if item.link and item.link != "" %}
          <a href="{{ item.link }}" target="_blank" rel="noopener noreferrer">
            <i class="fas fa-link"></i>
          </a>
          {% endif %}
        </div>
      </div>

  </div>

  {% endfor %}
{% else %}
<p>No items found. Check <code>_data/media.csv</code>.</p>
{% endif %}
</div>

<script>
(function () {

const state = {
topic: new Set(["all"]),
format: new Set(["all"])
};

const groups = document.querySelectorAll(".filter-group");
const items = Array.from(document.querySelectorAll(".pub-item"));
const countEl = document.getElementById("filterCount");
const clearBtn = document.getElementById("clearFilters");

function normalize(v){ return (v||"").trim(); }

function splitTags(v){
return normalize(v).split("|").map(x=>x.trim()).filter(Boolean);
}

function setActive(btn,val){
btn.classList.toggle("is-active",val);
}

function resetGroup(group){
state[group]=new Set(["all"]);
document.querySelectorAll(`[data-filter-group="${group}"] .pill`)
.forEach(b=>setActive(b,b.dataset.value==="all"));
}

function match(values,set){
if(set.has("all")) return true;
return values.some(v=>set.has(v));
}

function applyFilters(){

let shown=0;

items.forEach(item=>{

const topic=splitTags(item.dataset.topic);
const format=splitTags(item.dataset.format);

const ok =
match(topic,state.topic) &&
match(format,state.format);

item.style.display = ok ? "" : "none";

if(ok) shown++;

});

countEl.textContent=`Showing ${shown} of ${items.length} items`;

}

groups.forEach(group=>{

const groupName=group.dataset.filterGroup;

group.addEventListener("click",function(e){

const btn=e.target.closest("button.pill");
if(!btn) return;

const val=normalize(btn.dataset.value);

if(val==="all"){
resetGroup(groupName);
applyFilters();
return;
}

if(state[groupName].has("all")){
state[groupName].delete("all");
}

if(state[groupName].has(val)){
state[groupName].delete(val);
setActive(btn,false);
}else{
state[groupName].add(val);
setActive(btn,true);
}

if(state[groupName].size===0){
resetGroup(groupName);
}else{
const allBtn=group.querySelector(`[data-value="all"]`);
if(allBtn) setActive(allBtn,false);
}

applyFilters();

});

});

clearBtn.addEventListener("click",function(e){
e.preventDefault();
resetGroup("topic");
resetGroup("format");
applyFilters();
});

applyFilters();

})();
</script>
