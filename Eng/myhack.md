# List of adjustment for this webpage

### In order to have multiple taxonomies  
For examples, I wanted to create a separate taxonomy for "People" and "Education".  
Then, I need to define those taxonomies in 'config.toml'.

First, I have to list all the taxonomies being used in the webpage.
```
[taxonomies]
    category = "categories"
    team = "teams"
    education = "educations"
```
Then, add new taxonomies to the switch.
```
[params.widgets]
    categories = true
    tags = false
    search = true
    teams = true
    educations = true
```

Then we use these new taxonomies in the front matter of the contents. For example, in ```content/people/faculty.md```,

```
+++
title = "Faculty"
date = "2015-08-03T13:39:46+02:00"
teams = ["faculty"]
+++
```

Now, we need to add html templates for new taxonomies.
In the 'universal' theme, the block for the taxonomy is treated as the widget that is located in ```layouts/partials/widgets/```.
So I duplicated ```categories.html``` in that directory and named it as ```teams.html``` and ```educations.html```.
Then we need to edit those files by replacing ```categories``` with ```teams``` or ```educations```.  
For example, the ```teams.html``` looks like
```
{{ if .Site.Params.widgets.teams }}
{{ if isset .Site.Taxonomies "teams" }}
{{ if not (eq (len .Site.Taxonomies.teams) 0) }}
<div class="panel panel-default sidebar-menu">

    <div class="panel-heading">
      <h3 class="panel-title">People</h3>
    </div>

    <div class="panel-body">
        <ul class="nav nav-pills nav-stacked">
            {{ range $name, $items := .Site.Taxonomies.teams }}
            <li><a href="{{ $.Site.BaseURL }}teams/{{ $name | urlize | lower }}">{{ $name }} ({{ len $items }})</a>
            </li>
            {{ end }}
        </ul>
    </div>
</div>
{{ end }}
{{ end }}
{{ end }}
```
I hardcoded the title of the taxonomy as "People".  

Now, we need to edit the layout html files.
The ```sidebar.html``` under ```layouts/partials``` looks like,

```
{{ if or (isset .Params "categories") (eq .Type "categories") }}
{{ partial "widgets/categories.html" . }}
{{ end }}

{{ if or (isset .Params "teams") (eq .Type "teams") }}
{{ partial "widgets/teams.html" . }}
{{ end }}

{{ if or (isset .Params "educations") (eq .Type "educations") }}
{{ partial "widgets/educations.html" . }}
{{ end }}

{{ partial "widgets/tags.html" . }}

{{ partial "widgets/search.html" . }}
```
So, there will be a list of category taxonomy values when there is "categories" in the front matter, or the type of the content is "categories". The second one is necessary for the webpage for chosen taxonomy value to have the sidebar.

When the taxonomy is selected, we want to have a list page dedicated for that taxonomy. To do this, I need to modify the line number 40 in ```layout/_default/list.html``` to
```
{{ $paginator := (.Paginate .Data.Pages) }}
```
Then, this html script will make a list page for a new taxonomy.
Also, I added
```
{{ if isset .Params "teams" }}
{{ if gt (len .Params.teams) 0 }}
in <a href="{{ $.Site.BaseURL }}teams/{{ index .Params.teams 0 | urlize | lower }}">{{ index .Params.teams 0 }}</a>
{{ end }}
{{ end }}
```
right below the block for "categories" to the same file to get the name of the new taxonomy in the summary of the contents.

We want to have an appropriate sidebar depending on the section.
For example, we want to have "People" sidebar under "People" pages.
To do this, we need to create "_index.md" and add the front matter like this.

```
+++
title = "People"
teams = []
+++
```
