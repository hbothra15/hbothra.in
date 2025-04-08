---
{"dg-publish":true,"comments":true,"tags":["utilities","quick_hack"],"dg-metatags":{"description":"Quick utils to expedite your work","title":"Quick CLI Utils","og:title":"Quick CLI Utils","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["hacks","cli","utilis","python","shell"],"og:article:section":"Technology"},"permalink":"/general/quick-cli-utils/","metatags":{"description":"Quick utils to expedite your work","title":"Quick CLI Utils","og:title":"Quick CLI Utils","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["hacks","cli","utilis","python","shell"],"og:article:section":"Technology"},"dgPassFrontmatter":true}
---

## Format Huge JSON File
When IDE doesn't support the formatting of a file that is huge we can use the below python command to get it formatted

`python3 -m json.tool unformatted.json > formatted.json`

## Finding Duplicate records based on certain attributes in JSON

Say you have a huge record in JSON, and you want to do a quick check to find duplicate records using certain fields in JSON's Object Array

` jq '.[] | .email' data.json | sort | uniq -d`