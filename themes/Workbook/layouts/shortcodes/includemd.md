{{/* This includes a markdown file (without frontmatter) inline
   * DO NOT USE THIS FOR PAGES (it's only for things withough frontmatter)
   *
   * originally from 559Theme (duplicated)
   */}}
{{- $file := .Get 0 -}}
{{- $dir := .Page.File.Dir -}}
{{- $fullname := (printf "%s/%s" $dir $file)}}
{{- readFile $fullname -}}