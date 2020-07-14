{{ with $.Page.Resources.GetMatch (.Get "src") -}}
[<i class='fas fa-download fa-fw'></i>{{" "}}
{{- with $.Get "pre" }}{{.}} {{ end }}
{{- .Title -}}
{{ with $.Get "post" }}{{.}} {{ end }}]({{- .RelPermalink }})
{{- end }}