---
layout: page
title: Optimizing Geoguessr Plonks
description: Ever wondered where exactly the highest EV Plonks are?
img: assets/img/Poland_Plonk.png
importance: 2
category: Upcoming
---

Geoguessr players oftentimes find themselves in a bind where they may know the country they're meant to be guessing, but cannot seem to work out which region they should head for. No region guess? No Problem! Through this project, I seek to find the exact location of the plonk with the highest Expected Value (EV) in each country represented in the competition map, <i>A Moving World</i> by user BojanR. 

**Tech:** Python 

<i>Full article coming soon!</i>

<!--[Read the full article →](/blog/2026/MM26-RFA/)-->


<div style="margin-top: 2rem; margin-bottom: 2rem;">
  <div style="display: flex; justify-content: space-between; align-items: baseline; margin-bottom: 0.5rem;">
    <strong>Dataset progress</strong>
    <span id="location-progress-text">0 / 20,914 locations (0.0%)</span>
  </div>

  <div style="
    width: 100%;
    height: 14px;
    background-color: #e9ecef;
    border-radius: 7px;
    overflow: hidden;
  ">
    <div id="location-progress-bar" style="
      width: 0%;
      height: 100%;
      background-color: #007bff;
      border-radius: 7px;
      transition: width 0.5s ease;
    "></div>
  </div>

  <div style="
    margin-top: 0.5rem;
    font-size: 0.85rem;
    color: #6c757d;
  ">
    Target: 20,914 mapped locations
  </div>
</div>

<script>
  const mappedLocations = 1195; // <-- CHANGE THIS NUMBER

  const targetLocations = 20914;
  const percentage = Math.min((mappedLocations / targetLocations) * 100, 100);

  document.getElementById("location-progress-bar").style.width =
    percentage.toFixed(2) + "%";

  document.getElementById("location-progress-text").textContent =
    mappedLocations.toLocaleString() +
    " / " +
    targetLocations.toLocaleString() +
    " locations (" +
    percentage.toFixed(1) +
    "%)";
</script>
