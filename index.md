---
layout: page
title: Open Source Web and Finance
tagLine: The Good Parts
---
{% include JB/setup %}

<h3>{{page.tagLine}}</h3>
<div class="container">
	<div class="row">
		<div class="col-md-4">
			<h2>Client-side</h2>
			{% assign posts_collate = site.categories.client-side %}
			{% include JB/posts_collate %}
		</div>
		<div class="col-md-4">
			<h2>Server-side</h2>
			{% assign posts_collate = site.categories.server-side %}
			{% include JB/posts_collate %}
	 	</div>
		<div class="col-md-4">
			<h2>Data Visualizations</h2>
			{% assign posts_collate = site.categories.data-vis %}
			{% include JB/posts_collate %}
		</div>
	</div>
	<hr>
</div>
