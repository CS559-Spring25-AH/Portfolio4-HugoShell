<ul>
{{ range (where .Site.RegularPages "Section" "docs") }}
    <li> <a href="{{ .RelPermalink }}">{{ .File.BaseFileName | humanize }} </li>
{{ end }}
</ul>