---
draft: true
title: 'Botón descarga CV'
---
## Educación

{{ range .Site.Params.education.items }}
### {{ .title }}
- **Escuela**: [{{ .school.name }}]({{ .school.url }})
- **Fecha**: {{ .date }}
{{ end }}

{{ if .Site.Params.education.button.enable }}
  <a href="{{ .Site.Params.education.button.url }}" class="btn btn-primary" 
     {% if .Site.Params.education.button.download %} download {% endif %}>
    {{ .Site.Params.education.button.name }}
  </a>
{{ end }}