{{ with $.Page.Resources.GetMatch (.Get "src") -}}
{{ if eq ($.Get "download") "top" -}}
[<i class='fas fa-download fa-fw'></i> Download {{.Title}}]({{ .RelPermalink }})
{{ end -}}
```{{.MediaType.SubType}}{{with $.Get "hl_lines"}} {hl_lines={{. | safeHTML}}} {{end}}
{{ .Content | safeHTML }}
```
{{- if eq ($.Get "download") "bottom"}}
[<i class='fas fa-download fa-fw'></i> Download {{.Title}}](
{{- .RelPermalink }}){{end}}
{{- end }}