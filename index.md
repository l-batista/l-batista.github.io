---
layout: page
title: Luis Batista
subtitle: People Analytics | AI Agents | Knowledge Graphs
cover-img: "/cover.gif"
---

# Hi, I'm Luis Batista

I've worked in People Analytics across many organizations, including at Johnson & Johnson. I build AI solutions that turn
workforce and business data into decisions. My current focus is knowledge management, graph databases, GraphRAG, and AI agents.

## What I Bring

- **People Analytics leadership:** 12 years across J&J, Mastercard, L3Harris, and New York Life
- **Technical builder:** Tableau, PowerBI, Power Automate, SQL, Neo4j/Cypher, Langflow agents 
- **Business translator:** MBA in Analytics, turning complex data into executive-ready insight. Delivering insights that drive decisions to senior stakeholders

## Featured Projects

{% assign featured = site.data.projects | where: "featured", true %}
{% include project-cards.html projects=featured carousel=true %}

[View all projects →](/projects)

## Featured Tools

{% include project-cards.html projects=site.data.tools link_text="Explore the tool" %}

<script>
document.querySelectorAll('.is-carousel').forEach(function (c) {
  var track = c.querySelector('.project-grid');
  c.querySelector('.prev').onclick = function () { track.scrollBy({ left: -track.clientWidth * 0.9, behavior: 'smooth' }); };
  c.querySelector('.next').onclick = function () { track.scrollBy({ left: track.clientWidth * 0.9, behavior: 'smooth' }); };
});
</script>

## Contact

- [LinkedIn](https://www.linkedin.com/in/luisgbatista/)
- [Tableau](https://public.tableau.com/app/profile/luisbatista)
- [GitHub](https://github.com/l-batista)
