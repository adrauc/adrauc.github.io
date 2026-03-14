---
layout: archive
title: "Peer-reviewed journal articles"
permalink: /publications/
author_profile: true
---

{% include base_path %}

<script>
document.addEventListener("DOMContentLoaded", function () {
  document.body.classList.add("publications-page");
});
</script>

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

<div class="filter-row">
  <div class="filter-label">METHOD:</div>
  <div class="filter-group" data-filter-group="method">
    <button type="button" class="pill is-active" data-value="all">All</button>
    <button type="button" class="pill" data-value="Experiment">Experiment</button>
    <button type="button" class="pill" data-value="Social Network Analysis">Social Network Analysis</button>
    <button type="button" class="pill" data-value="Text Analysis">Text Analysis</button>
    <button type="button" class="pill" data-value="Survey Data">Survey Data</button>
    <button type="button" class="pill" data-value="Platform Data">Platform Data</button>
    <button type="button" class="pill" data-value="Theory">Theory</button>
  </div>
</div>

<div class="filter-row">
  <div class="filter-label">CONTEXT:</div>
  <div class="filter-group" data-filter-group="context">
    <button type="button" class="pill is-active" data-value="all">All</button>
    <button type="button" class="pill" data-value="Cross-national">Cross-national</button>
    <button type="button" class="pill" data-value="US">US</button>
    <button type="button" class="pill" data-value="Taiwan">Taiwan</button>
    <button type="button" class="pill" data-value="China">China</button>
    <button type="button" class="pill" data-value="Switzerland">Switzerland</button>
    <button type="button" class="pill" data-value="Germany">Germany</button>
  </div>
</div>

<div class="filter-meta">
  <span id="filterCount"></span>
  <button type="button" class="pill pill-clear" id="clearFilters">Clear</button>
</div>

<hr>

{% assign pubs = site.data.publications | default: empty %}
{% assign pubs = pubs | sort: "year" | reverse %}

<div id="pubList">
{% if pubs.size > 0 %}
  {% for pub in pubs %}

  <div class="pub-item"
       data-topic="{{ pub.topic | escape }}"
       data-method="{{ pub.method | escape }}"
       data-context="{{ pub.context | escape }}">

      <div class="pub-year">
        {{ pub.year | default: "—" }}
      </div>

      <div class="pub-main">
        <p class="pub-title"><strong>{{ pub.title }}</strong></p>

        <p class="pub-authors">
          {{ pub.authors
             | replace: "Adrian Rauchfleisch", "<span class='pi-name'>Adrian Rauchfleisch</span>"
          }}
        </p>

        <p class="pub-journal">
          {{ pub.journal }}
        </p>

        <div class="pub-icons">

          {% if pub.link and pub.link != "" %}
          <a href="{{ pub.link }}" target="_blank" rel="noopener noreferrer">
            <i class="fas fa-link"></i>
          </a>
          {% endif %}

          {% if pub.pdf and pub.pdf != "" %}
          <a href="{{ pub.pdf | relative_url }}" target="_blank" rel="noopener noreferrer">
            <i class="fas fa-file-pdf"></i>
          </a>
          {% endif %}

          {% if pub.code and pub.code != "" %}
          <a href="{{ pub.code }}" target="_blank" rel="noopener noreferrer">
            <i class="fas fa-code"></i>
          </a>
          {% endif %}

        </div>
      </div>

  </div>

  {% endfor %}
{% else %}
<p>No publications found. Check <code>_data/publications.csv</code>.</p>
{% endif %}
</div>

<script>
(function () {

const state = {
topic: new Set(["all"]),
method: new Set(["all"]),
context: new Set(["all"])
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
const method=splitTags(item.dataset.method);
const context=splitTags(item.dataset.context);

const ok =
match(topic,state.topic) &&
match(method,state.method) &&
match(context,state.context);

item.style.display = ok ? "" : "none";

if(ok) shown++;

});

countEl.textContent=`Showing ${shown} of ${items.length} papers`;

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
resetGroup("method");
resetGroup("context");
applyFilters();
});

applyFilters();

})();
</script>