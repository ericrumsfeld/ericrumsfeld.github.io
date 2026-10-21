---
layout: page
title: NBA Draft Player Movement Tracker
description: Interactive visualization of NBA Draft prospect rankings across multiple seasons.
img: assets/img/Aday_Mara_Movement_2026.png
importance: 1
category: interactive
---


<script src="https://cdn.plot.ly/plotly-3.3.0.min.js"></script>

<div style="display:flex; gap:20px; flex-wrap:wrap; margin-bottom:20px;">
  <div>
    <label for="player">Player</label>
    <select id="player" style="width:230px;"></select>
  </div>
  <div>
    <label for="season">Season</label>
    <select id="season" style="width:150px;"></select>
  </div>
</div>

<div id="movement-plot" style="width:100%; min-height:550px;"></div>

<script>
(async function () {
  const response = await fetch(
    "{{ '/assets/data/boards.json' | relative_url }}"
  );

  if (!response.ok) {
    document.getElementById('movement-plot').textContent =
      'Could not load ranking data.';
    return;
  }

  const boards = await response.json();

  const playerSelect = document.getElementById('player');
  const seasonSelect = document.getElementById('season');

  const seasons = [...new Set(boards.map(d => d.season))].sort();

  seasons.forEach(season => {
    seasonSelect.add(new Option(season, season));
  });

  seasonSelect.value = seasons.includes('2024-2025')
    ? '2024-2025' : seasons[seasons.length - 1];

  function updatePlayers() {
    const players = [...new Set(
      boards.filter(d => d.season === seasonSelect.value)
            .map(d => d.fullName)
    )].sort();

    const previous = playerSelect.value;
    playerSelect.replaceChildren();

    players.forEach(player => {
      playerSelect.add(new Option(player, player));
    });

    playerSelect.value = players.includes(previous)
      ? previous
      : players.includes('Cedric Coward')
        ? 'Cedric Coward'
        : players[0];

    drawPlot();
  }

  function drawPlot() {
    const season = seasonSelect.value;
    const player = playerSelect.value;

    const seasonData = boards.filter(d => d.season === season);
    const playerData = seasonData
      .filter(d => d.fullName === player)
      .sort((a, b) => a.boardDate.localeCompare(b.boardDate));

    // Background: each other player's trajectory
    const otherPlayers = [...new Set(
      seasonData.map(d => d.fullName)
    )].filter(name => name !== player);

    const traces = otherPlayers.map(name => {
      const rows = seasonData
        .filter(d => d.fullName === name)
        .sort((a, b) => a.boardDate.localeCompare(b.boardDate));

      return {
        x: rows.map(d => d.boardDate),
        y: rows.map(d => d.rank),
        type: 'scatter',
        mode: 'lines+markers',
        line: {color: 'rgba(150,150,150,0.18)', width: 1},
        marker: {color: 'rgba(150,150,150,0.18)', size: 3},
        hoverinfo: 'skip',
        showlegend: false
      };
    });

    // Foreground: selected player
    traces.push({
      x: playerData.map(d => d.boardDate),
      y: playerData.map(d => d.rank),
      customdata: playerData.map(d => [d.source]),
      type: 'scatter',
      mode: 'lines+markers',
      name: player,
      line: {color: '#b91c1c', width: 2.5},
      marker: {color: '#b91c1c', size: 8},
      hovertemplate:
        '<b>' + player.replaceAll('&', '&amp;').replaceAll('<', '&lt;') + '</b><br>' +
        'Source: %{customdata[0]}<br>' +
        'Date: %{x}<br>' +
        'Rank: %{y}<extra></extra>'
    });

    const layout = {
      title: `${player} — ${season} Big Board Movement`,
      xaxis: {title: 'Big Board Date', type: 'date'},
      yaxis: {
        title: 'Player Rank',
        range: [105, 0]
      },
      shapes: [14, 30, 60].map(rank => ({
        type: 'line',
        xref: 'paper',
        x0: 0, x1: 1,
        y0: rank, y1: rank,
        line: {color: '#38bdf8', dash: 'dash', width: 1}
      })),
      margin: {l: 55, r: 25, t: 65, b: 55},
      hovermode: 'closest',
      showlegend: true
    };

    Plotly.react('movement-plot', traces, layout, {
      responsive: true,
      displaylogo: false
    });
  }

  seasonSelect.addEventListener('change', updatePlayers);
  playerSelect.addEventListener('change', drawPlot);

  updatePlayers();
})();
</script>


